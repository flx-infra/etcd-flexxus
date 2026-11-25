# etcd compartido · Swarm 10.250.0.98

Cluster etcd de 3 miembros que usa Patroni para saber quién es el primario de cada celda PostgreSQL. Uno solo para todo el Swarm: cada celda tiene su propio `scope` de Patroni (`celda-01`, `celda-02`...).

| Servicio | Nodo | Para qué |
|---|---|---|
| etcd-a | manager01 | miembro 1 |
| etcd-b | worker1 | miembro 2 |
| etcd-c | worker3 | miembro 3 |

- Se despliega en Portainer como stack `etcd-flexxus`, desde este repo (rama `main`), con actualización automática cada 5 minutos.
- Crea la red overlay `etcd-net`. Las celdas la declaran como externa y usan `PATRONI_ETCD3_HOSTS=etcd-a:2379,etcd-b:2379,etcd-c:2379`.
- Con 3 miembros soporta la caída de uno. Si caen dos, Patroni sigue con el primario actual gracias a `failsafe_mode`, pero no puede promover otro.
- Los nodos se pueden cambiar con `ETCD_NODE_A`, `ETCD_NODE_B` y `ETCD_NODE_C`. Cambiar el nodo de un miembro existente pierde sus datos: se hace de a uno, quitándolo y agregándolo con `etcdctl member`.
- Cuidado al tocar este stack: lo usan todas las celdas.

Respaldo: `etcdctl --endpoints=http://etcd-a:2379 snapshot save /etcd-data/snapshot.db` (pendiente de automatizar).
