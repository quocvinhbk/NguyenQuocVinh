# Solution for problem 3: Diagnose Me Doctor

## A. First, make sure that only NGINX is consuming high memory on the server.
    - Use `htop` to confirm that NGINX is the service consuming the most memory.
    - Remote access to the VM is difficult and may take a long time due to very high memory usage.

## B. Once we have confirmed that NGINX is the main cause of high memory usage:
Since the issue occurred when the system was previously running normally, we prioritize the troubleshooting steps based on experience:
1. No Log Rotation
2. DDoS Attack
3. Backend Service Failure
4. Nginx Configuration Issues

### 1. No Log Rotation
    - Reason: If Nginx logs become too large (several GBs), they can consume a lot of RAM due to system buffering.
    - How to check:
    ```sh
    ls -lh /var/log/nginx/
    ```
    - Solution: Enable log rotation to prevent excessive log growth.

### 2. DDoS Attack
    - How to check:
    1. Check the number of active connections to Nginx:
        ```sh
        netstat -anp | grep ':80' | wc -l
        ```
    2. Analyze the access log to identify IPs with an unusually high number of requests:
        ```sh
        cat /var/log/nginx/access.log | awk '{print $1}' | sort | uniq -c | sort -nr | head -20
        ```
    - Solution: Limit the number of connections and requests per IP using Nginx configuration.

### 3. Backend Service Failure
    - Reason: If the backend server is overloaded or crashes, Nginx may consume excessive RAM while waiting for responses.
    - How to check:
       - Look for repeated 502 Bad Gateway or 504 Gateway Timeout errors in `error.log`.
       - Manually check the backend service status.
    - Solution: Restart and ensure the backend service is running properly.

### 4. Nginx Configuration Issues:

#### 4.1 `proxy_cache` Unlimited (Cache Consumes Too Much RAM)
    - Reason: Initially, the cache may not consume much RAM, but over time, if `max_size` is not set, the cache will keep growing, eventually exhausting memory.
    - How to check:
     - Verify the `max_size` setting in `proxy_cache`.
    - Solution:
     - Set an appropriate `max_size` value for `proxy_cache`.

#### 4.2 `proxy_buffers` Too Large (Excessive Proxy Buffering)
    - Reason: If the backend service sends large responses, Nginx must hold this data in memory before forwarding it to clients. With increased traffic, memory usage escalates.
    - How to check:
     - Inspect the `proxy_buffers` configuration.

#### 4.3 `worker_connections` Too High (Managing Too Many Concurrent Connections)
    - Reason: If `worker_connections` is set too high, Nginx might allocate more RAM than necessary to handle excessive connections.
    - How to check:
     - Review the `worker_connections` setting.
    - Solution:
     - Adjust `worker_connections` to a reasonable value.

#### 4.4 `keepalive_requests` Too High (Connections Kept Open for Too Long)
    - Reason: If connections remain open for an extended period without closing, RAM usage will gradually increase over time.
    - How to check:
     - Check the `keepalive_requests` setting.
    - Solution:
     - Adjust `keepalive_requests` to a reasonable limit.

### Conclusion: The two most likely causes of high memory usage in Nginx are:
1. Log rotation misconfiguration
2. Nginx configuration issues (e.g., caching, buffering, excessive connections)
