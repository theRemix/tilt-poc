# Globals
namespace="tilt-poc"

# Image Registry
default_registry("docker.io/theremix")

# Extensions
load("ext://git_resource", "git_resource")
load("ext://namespace", "namespace_create")

# Callbacks
def deployment(resource_name, image_name, namespace): # returns deployment definition yaml
  return blob(
    str(
      read_file("deployment.yml")
    ).format(
      resource_name = resource_name,
      image_name = image_name,
      namespace = namespace
    )
  )

def build(resource_name, context, dockerfile='Dockerfile'):  # returns resultant image name
  image_name = resource_name + '-image'
  docker_build(
    image_name,
    '%s/%s' % (context, 'nginx-hello'),
    dockerfile='%s/%s' % (context, dockerfile),
    ssh='default',
    live_update=[
      sync('.git-sources/NGINX-Demos/nginx-hello/index.html', '/usr/share/nginx/html/'),
      run("date >> /usr/share/nginx/html/index.html",
      trigger='.git-sources/NGINX-Demos/nginx-hello/index.html'),
    ]
  )
  return image_name

# Create Namespace
namespace_create(namespace)

# Deployments
git_resource(
  "tilt-poc",
  "https://github.com/nginxinc/NGINX-Demos",
  dockerfile="nginx-hello/Dockerfile",
  namespace=namespace,
  build_callback=build,
  deployment_callback=deployment,
  port_forwards=[],
)
