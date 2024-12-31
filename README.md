![A table piled high with classic American breakfast foods featuring fluffy scrambled eggs, golden pancakes stacked with melting butter and maple syrup](/public/hero.webp)

# README

The app currently doesn't do anything.

1. new Rails 8 app

- `rails new --css=tailwind --devcontainer`

2. upgraded to Tailwind v4 beta

- see [application.tailwind.css](/app/assets/stylesheets/application.tailwind.css)

3. setup actions

- see [publish.yml](/.github/workflows/publish.yml)
- see [deploy.yml](/.github/workflows/deploy.yml)
- see [packages](/pkgs/container/eatbreakfast.club)

4. registered domain through Cloudflare

- includes SSL, CDN, caching, etc.

5. hosted on DigitalOcean Kubernetes (DOKS)

- see [load_balancer.yml](/config/kube/load_balancer.yml)
- see [deployment.yml](/config/kube/deployment.yml)
- runs `kubectl rollout restart deployment/eatbreakfast-club-deployment`

## Other commands

- after making production infrastructure changes
  - `kubectl apply -f config/kube`
- open production rails console
  - `kubectl exec -it deployment/eatbreakfast-club-deployment -- bin/rails c`
- open production shell
  - `kubectl exec -it deployment/eatbreakfast-club-deployment -- /bin/bash`
- tail logs
  - `kubectl logs -f deployment/eatbreakfast-club-deployment`

## Diagram

```mermaid
sequenceDiagram
    participant B AS Browser
    participant P AS Proxy
    participant L AS LoadBalancer
    participant W AS WorkerNode
    participant Th AS Thruster
    participant Pu AS Puma
    rect rgba(255,96,0,0.5)
    Note over B,P: Cloudflare<br/>DNS/SSL
    B->>P: Encrypted traffic between browser
    end
    rect rgba(0,96,128,0.5)
    Note over P,Pu: DigitalOcean<br/>k8s
    P->>L: Routes traffic to WorkerNode(s)
    L->>W: Request
    rect rgba(128,0,0,0.5)
    Note over W,Pu: Ruby<br/>Rails
    W->>Th: Caching
    Th->>Pu: App
    end
    end
    Pu-->>B: Response
```
