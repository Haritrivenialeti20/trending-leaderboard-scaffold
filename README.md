# Trending Posts Leaderboard

Build a trending leaderboard backed by a Redis **sorted set** (ranking) + a **hash per post** (metadata), reading the whole board in **one pipelined round trip**.

## Setup

```bash
npm install
cp .env.example .env
docker start redis-m4      # or start the Redis service configured by REDIS_URL
```

## What to implement

- **`leaderboard.js`** — `upvote(postId)` (one atomic `ZINCRBY`) and `getTop(n)` (`ZREVRANGE` + one pipelined `HGETALL` per id).
- **`seed.js`** — load 100 fake posts (a hash per post + a sorted-set member each), using one pipeline.

## Run it

```bash
npm run seed    # loads 100 posts
npm test        # upvotes post:50 fifty times, asserts it reaches the top 10
```

Expected: the test prints the top 10 and ends with `PASS ✅`.

`seed.js` batches the sorted-set reset and all 200 metadata/ranking writes in one pipeline. `getTop(n)` performs one `ZREVRANGE` followed by a single pipeline containing all `HGETALL` metadata reads. Pipelining reduces round trips; it is not a transaction and does not make commands atomic.

## Submit

Fork → branch → commit → open a PR against your fork. Submit the PR link. Do **not** commit `.env` or `node_modules/`.
