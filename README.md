# Wan 3.0 API (`wan3.0-video`)

<!-- conv-kit:v1 -->

<p align="center">
  <img src="assets/badges/price.svg" alt="observed unit price"> <img src="assets/badges/billing.svg" alt="billing model"> <img src="assets/badges/compat.svg" alt="OpenAI-compatible endpoint">
</p>

<p align="center">
  <img src="assets/01-product-ad-ceramic-mug-thumb.jpg" width="820" alt="Sample video frame generated through APIMart">
</p>

> **from $0.0329 per second** on the APIMart route — 一个 OpenAI 兼容端点 `https://api.apimart.ai/v1`，无需包月套餐。*(数据观测于 2026-10-12)*

**[获取 API Key](https://go.apimart.ai/k-b7e2b3)** · **[实时价格](https://go.apimart.ai/k-136626)** · **[模型页面](https://go.apimart.ai/k-8b1d87)** · [⚡ 60 秒快速上手](#quickstart)

**为什么团队通过 APIMart 调用 Wan 3.0（`wan3.0-video`）**

- **一个 Key，整个目录。**同一个 `https://api.apimart.ai/v1` base URL 和 `Authorization` 请求头即可访问 Wan 3.0（`wan3.0-video`）以及图像、视频、语言等 300+ 模型——只需切换 `model` 字段，客户端无需改动。
- **$1 起充，按量付费。**没有订阅、也没有需要预先估算用量的预付费套餐：最低充值 $1，调用多少扣多少。
- **费用随响应一并返回。**每个完成的任务都会报告实际计费金额（`cost` / `credits_cost`）；状态为 `failed` 的任务会自动退款。
- **原生异步设计。**提交任务、拿到 `task_id`、轮询 `GET /v1/tasks/{id}`——批处理和重试都是普通的队列工作，不需要定制集成。

<!-- /conv-kit:v1 -->

**中文环境也叫:** 通义万相

Wan 3.0 API (Wan API) provides access to Alibaba Cloud's all-in-one video model. Wan API offers general, first-and-last-frame, and file or webpage generation. Wan API general generation accepts prompts with optional image, video, and audio references. Wan API frame generation requires a first frame and allows an optional last frame. One asynchronous Wan API endpoint creates 2–30 second videos at 480P, 720P, or 1080P with optional output audio.

## Model id and routes

| Route | `model` value | Billing | Notes |
| --- | --- | --- | --- |
| Text-to-video / image-to-video | `wan3.0-video` | per generated second, by resolution | see the [model page](https://go.apimart.ai/k-8b1d87) for the exact tiers |

Endpoint: `POST https://api.apimart.ai/v1/videos/generations`, then poll `GET /v1/tasks/{task_id}`.

## Pricing

<!-- pricing:model:start -->
| Resolution | List price | Effective price |
| --- | --- | --- |
| default | 0.16438 | 0.131504 |


<!-- pricing:model:end -->

Prices are a snapshot; the [pricing page](https://go.apimart.ai/k-136626) and [`data/model.json`](data/model.json) are refreshed by CI, and a completed task reports the exact amount in its `cost` field.

## Request parameters

| Field | Type | Default | Notes |
| --- | --- | --- | --- |
| `model` | string | required | the model id shown in the route table above |
| `prompt` | string | required | the shot description; camera and subject motion belong here |
| `duration` | integer | route default | clip length in seconds — billing is per generated second |
| `resolution` | string | route default | 480P / 720P / 1080P or the route’s own tiers |
| `size` / `aspect_ratio` | string | `16:9` | output framing |
| `image_urls` | string[] | — | first frame, first + last frame, or style references, where supported |
| `seed` | integer | — | fix the seed to reproduce a take |

The table lists the fields accepted by the APIMart `videos/generations` endpoint. Check the [model page](https://go.apimart.ai/k-8b1d87) for anything this model adds on top.

## Quickstart

```bash
curl --request POST --url https://api.apimart.ai/v1/videos/generations \
  --header "Authorization: Bearer $APIMART_API_KEY" --header 'Content-Type: application/json' \
  -d '{"model":"wan3.0-video","prompt":"A bamboo forest at sunrise, slow push-in","duration":5,"resolution":"720P","size":"16:9"}'
```

```python
import os, time, requests

BASE = "https://api.apimart.ai/v1"
HEADERS = {"Authorization": f"Bearer {os.environ['APIMART_API_KEY']}", "Content-Type": "application/json"}

created = requests.post(f"{BASE}/videos/generations", headers=HEADERS, timeout=60, json={
    "model": "wan3.0-video", "prompt": "A bamboo forest at sunrise, slow push-in", "duration": 5,
}).json()
task_id = created["data"]["id"]

while True:
    task = requests.get(f"{BASE}/tasks/{task_id}", headers=HEADERS, timeout=60).json()["data"]
    if task["status"] in ("completed", "failed"):
        break
    time.sleep(5)
print(task.get("cost"), task["result"]["videos"][0]["url"])
```

Runnable versions: [`examples/`](examples). The task lifecycle is `pending → processing → completed | failed`.

## FAQ

**Wan 3.0 的模型 id 是什么？**

`wan3.0-video`。见上方路由表。

**如何计费？**

**from $0.0329 per second** on the APIMart route。完成的任务会在 `cost` 字段中给出准确金额；失败的任务会自动退款。

**How do I get a key?**

Use the [Get an API key](https://go.apimart.ai/k-b7e2b3) link — $1 minimum top-up, no plan required.

<!-- related-repos:auto -->
## Related repositories

- **Same model, this language** — the other half of this pair: [wan3-0-prompts-cn](https://github.com/k1ec3xgah/wan3-0-prompts-cn)
- **Same model, other languages** — 10 more language builds: English [wan3-0-api](https://github.com/w8krjle/wan3-0-api) · Deutsch `wan3-0-api-de` · Español [wan3-0-api-es](https://github.com/fdaibaca5/wan3-0-api-es) · Français `wan3-0-api-fr` · Relay/gateway `wan3-0-api-gateway` · Bahasa [wan3-0-api-id](https://github.com/hbh32icepub90f/wan3-0-api-id) · 日本語 [wan3-0-api-ja](https://github.com/olqd721wfq43/wan3-0-api-ja) · 한국어 [wan3-0-api-ko](https://github.com/ev-77zgxu8t/wan3-0-api-ko) · Português [wan3-0-api-pt](https://github.com/v271v/wan3-0-api-pt) · Русский `wan3-0-api-ru`
- **More models in this language** — [midjourney-api-cn](https://github.com/apimartycl6k972t/midjourney-api-cn) · [gemini-2-5-flash-image-preview-api-cn](https://github.com/hm-5j/gemini-2-5-flash-image-preview-api-cn) · [nano-banana-api-cn](https://github.com/mpflv/nano-banana-api-cn) · [gemini-3-1-flash-image-preview-api-cn](https://github.com/utp00/gemini-3-1-flash-image-preview-api-cn) · [nano-banana-2-api-cn](https://github.com/i3hxnu6/nano-banana-2-api-cn) · [gemini-3-pro-image-preview-api-cn](https://github.com/vd0tv42d/gemini-3-pro-image-preview-api-cn)
- **Topic hubs** — every build of this model, all languages: [wan3-0](https://github.com/topics/wan3-0) · [wan3-0-api](https://github.com/topics/wan3-0-api)

This repository is one build in a cross-linked set of API guides for the same model family; each language build links back to the others.
<!-- /related-repos:auto -->

## Attributed links (how this repository is measured)

| Purpose | Attributed link | Target |
| --- | --- | --- |
| Model page | <https://go.apimart.ai/k-8b1d87> | `apimart.ai/zh/model/wan-3-0` |
| Live pricing | <https://go.apimart.ai/k-136626> | `apimart.ai/zh/pricing` |
| Get an API key | <https://go.apimart.ai/k-b7e2b3> | `apimart.ai/zh/keys` |

Outbound APIMart links are minted through the promo link API; hand-made tracking parameters are rejected by
`tools/check_links.py` in CI.

## Disclosure

Wan 3.0 is a third-party model served through APIMart; this repository documents how to call it and publishes
model ids and prices, and does not claim official status. Model names, prices and documentation belong to their
respective owners.

## License

MIT — see [LICENSE](LICENSE).
