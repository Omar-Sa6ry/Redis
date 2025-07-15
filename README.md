# 🧠 NestJS Redis Module

A fully featured, production-ready Redis module for NestJS with support for:

- ✅ Key-Value operations
- ✅ Hashes, Sets, Sorted Sets, Lists
- ✅ Pub/Sub
- ✅ Transactions
- ✅ Distributed Locks
- ✅ Health Check
- ✅ Auto JSON serialization
- ✅ Multi-layer support: `@nestjs/cache-manager` + native `ioredis` and `redis` client

---

## 📦 Installation

```bash
npm install --save cache-manager-ioredis redis
```

> Requires Node.js v18+ and Redis v6+

---

## 🔧 Environment Variables

```env
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_TTL=3600
REDIS_MAX_ITEMS=1000
```

---

## 📁 Folder Structure

```
redis/
├── config/
│   └── redisConfig.service.ts       # CacheModule config using ioredis
├── constant/
│   └── redis.constant.ts            # Shared enums/constants
├── factory/
│   └── redisClient.factory.ts       # Redis connection with reconnect strategy
├── interface/
│   └── redis.interface.ts           # IRedisInterface: all Redis operations typed
├── provider/
│   └── redisService.provider.ts     # Injects IRedisService token
├── redis.health.ts                  # Health check ping for Redis
├── redis.service.ts                 # Full implementation with Redis & cache
└── redis.module.ts                  # Main module exports
```

---

## 🚀 Usage

### 1. Import Module

```ts
// app.module.ts
import { RedisModule } from './redis/redis.module';

@Module({
  imports: [RedisModule],
})
export class AppModule {}
```

---

### 2. Inject & Use Redis Service

```ts
import { Inject, Injectable } from '@nestjs/common';
import { IRedisInterface } from './redis/interface/redis.interface';

@Injectable()
export class SomeService {
  constructor(
    @Inject('IRedisService')
    private readonly redisService: IRedisInterface,
  ) {}

  async useRedis() {
    await this.redisService.set('myKey', { hello: 'world' }, 60);
    const value = await this.redisService.get('myKey');
    console.log(value); // { hello: 'world' }
  }
}
```

---

## 🔐 Features

### ✅ Core Key-Value
- `set(key, value, ttl?)`
- `get(key)`
- `del(key)`
- `mSet({ key1: val1, key2: val2 })`

### ✅ Hash Operations
- `hSet`, `hGet`, `hGetAll`, `hExists`, `hDel`
- `hKeys`, `hVals`, `hLen`, `hIncrBy`, `hSetNX`

### ✅ Set / Sorted Set / List / Geo / PubSub / HyperLogLog

Refer to [`IRedisInterface`](./interface/redis.interface.ts) for full list of supported methods.

---

## ❤️ Distributed Locking (Advanced Redis)

The service supports safe distributed locks using:

- `acquireLock()`
- `releaseLock()`
- `extendLock()`
- `waitForLock()`

Useful for queue processing, cron tasks, race condition protection.

---

## 🩺 Health Check

```ts
// Call this in your health check route or scheduler
await this.redisService.ping(); // returns "PONG"
```

---

## 📚 Example: Multi Command Transaction

```ts
await this.redisService.withTransaction(['user:1'], (multi) => {
  multi.set('user:1', JSON.stringify({ name: 'Omar' }));
  multi.expire('user:1', 3600);
});
```
