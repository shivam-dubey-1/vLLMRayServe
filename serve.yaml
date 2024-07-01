apiVersion: ray.io/v1alpha1
kind: RayService
metadata:
  name: vllm-rayserve
spec:
  serviceUnhealthySecondThreshold: 60
  deploymentUnhealthySecondThreshold: 60
  serveConfigV2: |
    applications:
      - name: vllm
        import_path: llama3-model:build_app
        runtime_env:
          working_dir: "https://github.com/shivam-dubey-1/vLLMRayServe/archive/refs/heads/main.zip"
          pip: ["vllm==0.5.0", "ray==2.10.0"]
  rayClusterConfig:
    rayVersion: '2.10.0' 
    headGroupSpec:
      serviceType: LoadBalancer
      rayStartParams:
        dashboard-host: '0.0.0.0'
      template:
        spec:
          containers:
            - name: ray-head
              image: rayproject/ray-ml:2.10.0-gpu
              ports:
                - containerPort: 6379
                  name: gcs
                - containerPort: 8265
                  name: dashboard
                - containerPort: 10001
                  name: client
                - containerPort: 8000
                  name: serve
              volumeMounts:
                - mountPath: /tmp/ray
                  name: ray-logs
              resources:
                limits:
                  cpu: 2
                  memory: 12Gi
                  # nvidia.com/gpu: 1
                requests:
                  cpu: 2
                  memory: 12Gi
                  # nvidia.com/gpu: 1
          volumes:
            - name: ray-logs
              emptyDir: {}
    workerGroupSpecs:
      - replicas: 1
        minReplicas: 1
        maxReplicas: 4
        groupName: small-group
        rayStartParams: {}
        template:
          spec:
            containers:
              - name: ray-worker
                image: rayproject/ray-ml:2.10.0-gpu
                resources:
                  limits:
                    cpu: 2
                    memory: "12G"
                    nvidia.com/gpu: 1
                  requests:
                    cpu: 2
                    memory: "12G"
                    nvidia.com/gpu: 1
