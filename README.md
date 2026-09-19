# Schwabdev
![PyPI - Version](https://img.shields.io/pypi/v/schwabdev) ![Discord](https://img.shields.io/discord/1076596998150561873?logo=discord) ![PyPI - Downloads](https://img.shields.io/pypi/dm/schwabdev) [![Donate](https://img.shields.io/badge/Donate-PayPal-green.svg)](https://www.paypal.com/donate/?business=8VDFKHMBFSC2Q&no_recurring=0&currency_code=USD) ![YouTube Video Views](https://img.shields.io/youtube/views/kHbom0KIJwc?style=flat&logo=youtube)  
Schwabdev is an easy and lightweight python wrapper for using the Charles Schwab API.   
This package is not affiliated with or endorsed by Schwab, it is maintained by [Tyler Bowers](https://github.com/tylerebowers) & [Contributors](https://github.com/tylerebowers/Schwabdev/graphs/contributors).   
Licensed under the MIT license. Acts in accordance with Schwab's API terms and conditions.  

### Setup Guide: <a target="_blank" href="https://tylerebowers.github.io/Schwabdev/?source=pages%2Fsetupguide.html">Start Here</a>

Useful links:

* Join the <a target="_blank" href="https://discord.gg/m7SSjr9rs9">Discord</a> to ask questions or get help.
* Read the <a target="_blank" href="https://tylerebowers.github.io/Schwabdev/">Documentation</a>.
* Watch the <a target="_blank" href="[https://youtu.be/69cniU1CTf8](https://www.youtube.com/watch?v=69cniU1CTf8&list=PLs4JLWxBQIxpbvCj__DjAc0RRTlBz-TR8)">Youtube</a> tutorials.
* Chat with the Schwabdev <a target="_blank" href="https://claude.ai/public/artifacts/32a686ef-e1bf-4861-9b8e-17327ce05f94">Claude</a> assistant.
* View the <a target="_blank" href="https://pypi.org/project/schwabdev/">PyPi</a> package page.
* View the <a target="_blank" href="https://github.com/tylerebowers/Schwab-API-Python">Github</a> repository.


### What can this program do?
- Automatic token management and refreshes.  
- Authenticate and access the full api with minimal code. <a target="_blank" href="https://github.com/tylerebowers/Schwabdev/tree/main/docs/examples/api_demo.py">Examples</a>.  
- Stream real-time data with a customizable response handler <a target="_blank" href="https://github.com/tylerebowers/Schwabdev/tree/main/docs/examples/stream_demo.py">Examples</a>.  
- Place orders and get order details <a target="_blank" href="https://tylerebowers.github.io/Schwabdev/?source=pages%2Forders.html">Examples</a>. 
- Backtest and deploy strategies with the <a target="_blank" href="https://tylerebowers.github.io/Schwabdev/?source=pages%2Ftc_details.html">Trader Context submodule</a>. (Beta)
- Support for Synchronous and Asynchronous programming <a target="_blank" href="https://github.com/tylerebowers/Schwabdev/tree/main/docs/examples/async_api_calls.py">Examples</a>. 
- Optional token database encryption for security <a target="_blank" href="https://github.com/tylerebowers/Schwabdev/blob/main/docs/examples/extra/encrypted_db_setup.py">Example</a>.
- Optional automatic starting/stopping of streamer when market opens/closes.  
- Streaming stability with automatic restarts if the streamer crashes.  

### Authentication & Token Storage
Schwabdev handles the OAuth flow and token persistence for you (`schwabdev/tokens.py`).

**OAuth flow**
1. Credentials (`app_key`, `app_secret`, `callback_url`) come from constructor args, falling back to `~/.schwabdev/env.json`.
2. **Initial auth (authorization-code grant)** — if no valid tokens exist yet, a browser is opened (or your custom `call_for_auth` callback is invoked) to `https://api.schwabapi.com/v1/oauth/authorize`, and you paste back the redirected callback URL. The `code` in that URL is exchanged for tokens via `POST /v1/oauth/token` with `grant_type=authorization_code`.
3. **Ongoing refresh** — on each use:
   - The **access token** lives 30 minutes and is refreshed automatically via `grant_type=refresh_token` once it's within 61 seconds of expiring.
   - The **refresh token** lives 7 days; once it's within ~60.5 minutes of expiring, the full browser authorization-code flow is triggered again (Schwab requires re-authentication rather than silently refreshing the refresh token).

**Local storage**
Tokens are stored in a SQLite database, default path `~/.schwabdev/tokens.db` (configurable via `tokens_db`), in a single-row `schwabdev` table (`access_token`, `refresh_token`, `id_token`, issued timestamps, `expires_in`, `token_type`, `scope`). On every update, the row is deleted and reinserted with the new values.

- **Optional encryption at rest** — pass a Fernet key via the `encryption` argument and `access_token`/`refresh_token`/`id_token` are encrypted with `cryptography.fernet.Fernet` before being written, prefixed with `enc:`. See the <a target="_blank" href="https://github.com/tylerebowers/Schwabdev/blob/main/docs/examples/extra/encrypted_db_setup.py">encrypted DB example</a>. Without a key, tokens are stored in plaintext in the SQLite file.
- **Multi-process safety** — updates use `BEGIN EXCLUSIVE` SQLite transactions plus a lock, so if multiple `Client` instances share the same DB file, only one refreshes over the network while the others detect the update and reload from disk.
- `app_key`/`app_secret` themselves are not persisted by this module — they stay in memory / `env.json` and are only used to sign the Basic Auth header on token requests.

### Development Setup (conda)
This repo includes an `environment.yml` for managing a conda virtual environment.

1. Create the environment: `conda env create -f environment.yml`
2. Activate it: `conda activate schwabdev`
3. Install the package in editable mode using [conda-pypi](https://github.com/conda/conda-pypi) (instead of `pip`): `conda pypi -n schwabdev -y install -e .`

### How to Contribute
1. Fork this repository.
2. Create a branch off of `dev` for your change (e.g. `git checkout -b my-fix dev`).
3. Make your change and commit it with a clear, descriptive message.
4. Push your branch to your fork.
5. Open a Pull Request **against the `dev` branch** (not `main`) of this repository.

Keep each PR focused on a single change so it's easy to review. 
If you're fixing a bug or adding a feature, a short description of the problem/motivation in the PR description helps a lot.

### MIT License

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
