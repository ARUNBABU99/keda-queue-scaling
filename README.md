# keda-queue-scaling
A small hands-on demo to understand how Kubernetes scales pods based on real workload using KEDA. Instead of CPU/memory, pods scale from a Redis queue (0 → N → 0). Built and tested locally on Minikube to learn how background worker systems actually autoscale.
