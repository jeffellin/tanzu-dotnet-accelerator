load('.tanzu/tanzu_tilt_extensions.py', 'tanzu_k8s_yaml')


SOURCE_IMAGE = os.getenv("SOURCE_IMAGE", default='your-registry.io/project/tanzu-dotnet')
LOCAL_PATH = os.getenv("LOCAL_PATH", default='.')

local_resource(
      'build',
      'dotnet publish src --configuration Release --runtime ubuntu.18.04-x64 --self-contained false --output ./build',
      deps=['src'],
      ignore=['src/bin','src/obj']
)

custom_build('your-registry.io/project/tanzu-dotnet',
    "tanzu apps workload apply -f config/workload.yaml --live-update \
      --local-path " + LOCAL_PATH + " --source-image " + SOURCE_IMAGE + " --yes && \
    .tanzu/wait.sh tanzu-dotnet",
  ['./build'],
  live_update = [ 
    sync('./build', '/workspace/build'),
    run('cp -rf /workspace/build/* /workspace', trigger='./build')
  ],
  skips_local_docker=True
  
)
tanzu_k8s_yaml('tanzu-dotnet', 'your-registry.io/project/tanzu-dotnet', './config/workload.yaml')
