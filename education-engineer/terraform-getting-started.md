# Getting Started with Terraform

Terraform is the most popular Infrastructure as Code (IaC) software tool for defining and provisioning resources across multiple cloud providers.

The instructions below will guide you through installing Terraform, along with the process of creating and destroying
 an example infrastructure. 

## Prerequisites

A computer with [Docker Desktop](https://www.docker.com/products/docker-desktop) installed, running on one of the
 following operating systems:
 * macOS
 * Windows
 * Linux
 * FreeBSD
 * OpenBSD
 * Solaris

## Install Terraform

Terraform is compatible with multiple operating systems and can be downloaded and installed as a binary package. 

To install Terraform, navigate to [Terraform.io](https://www.terraform.io/downloads.html). Then, download and unzip the binary package that is appropriate for your operating system. 

Make sure the terraform binary is available on your PATH. This process will differ depending on your operating system. 

## Create a configuration

With Terraform, you can provision resources by building a configuration file that details your desired
 infrastructure.

To do so, we recommend you create a new working directory at any location on your local machine to store your configuration
 file. For example, you will create a `terraform-demo` folder by typing the following Linux-based commands in your command prompt.
```shell
$ mkdir terraform-demo
$ cd terraform-demo
```

Next, create your Terraform configuration file. In this example, we name the configuration file `main.tf`.
```shell
$ touch main.tf
```

Open `main.tf` using any text editor and paste the following lines into the file. 
```hcl
terraform {
  required_providers {
    docker = {
      source = "kreuzwerker/docker"
    }
  }
}
provider "docker" {
    host = "unix:///var/run/docker.sock"
}
resource "docker_container" "nginx" {
  image = docker_image.nginx.latest
  name  = "training"
  ports {
    internal = 80
    external = 80
  }
}
resource "docker_image" "nginx" {
  name = "nginx:latest"
}
```
This configuration sets an `NGINX Docker Image` to a container, serving as a web server resource. Save your changes.

## Initialize and validate the configuration

After writing your configuration, you need to initialize the working directory. Doing so will also download any
 missing plugins (also known as providers) specified in your file.

You can initialize your configuration's working directory with the `terraform init` command. Terraform will now install the `Docker` provider for you. 
```shell
$ terraform init

Initializing the backend...

Initializing provider plugins...
- Reusing previous version of kreuzwerker/docker from the dependency lock file
- Using previously-installed kreuzwerker/docker v2.16.0

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

Run the `terraform validate` command to check for syntax or consistency errors within your configuration. 
```shell
$ terraform validate
Success! The configuration is valid.
```

## Create the infrastructure

If the previous steps run successfully, you can now create your infrastructure. Terraform will plan and apply your
 configuration. 

Provision the resources with the `terraform apply` command below. This step will take up to a few minutes to run as the resources Terraform plans to add, change, or destroy are displayed.
```shell
$ terraform apply

Terraform used the selected providers to generate the following execution plan. Resource actions are
indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # docker_container.nginx will be created
  + resource "docker_container" "nginx" {
      + attach           = false
      + bridge           = (known after apply)
      + command          = (known after apply)
      + container_logs   = (known after apply)
      + entrypoint       = (known after apply)
      + env              = (known after apply)
      + exit_code        = (known after apply)
      + gateway          = (known after apply)
      + hostname         = (known after apply)
      + id               = (known after apply)
      + image            = (known after apply)
      + init             = (known after apply)
      + ip_address       = (known after apply)
      + ip_prefix_length = (known after apply)
      + ipc_mode         = (known after apply)
      + log_driver       = (known after apply)
      + logs             = false
      + must_run         = true
      + name             = "training"
      + network_data     = (known after apply)
      + read_only        = false
      + remove_volumes   = true
      + restart          = "no"
      + rm               = false
      + security_opts    = (known after apply)
      + shm_size         = (known after apply)
      + start            = true
      + stdin_open       = false
      + tty              = false

      + healthcheck {
          + interval     = (known after apply)
          + retries      = (known after apply)
          + start_period = (known after apply)
          + test         = (known after apply)
          + timeout      = (known after apply)
        }

      + labels {
          + label = (known after apply)
          + value = (known after apply)
        }

      + ports {
          + external = 80
          + internal = 80
          + ip       = "0.0.0.0"
          + protocol = "tcp"
        }
    }

  # docker_image.nginx will be created
  + resource "docker_image" "nginx" {
      + id          = (known after apply)
      + latest      = (known after apply)
      + name        = "nginx:latest"
      + output      = (known after apply)
      + repo_digest = (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: 
```

Look for a message at the bottom of the output asking for confirmation. Type `yes` to apply the proposed plan and hit
 ENTER. Terraform will begin to apply the proposed changes. 
 ```shell
  Enter a value: yes

docker_image.nginx: Creating...
docker_image.nginx: Creation complete after 8s [id=sha256:c919045c4c2b0b0007c606e763ed2c830c7b1d038ce878a3c0d6f5b81e6ab80bnginx:latest]
docker_container.nginx: Creating...
docker_container.nginx: Creation complete after 0s [id=44a2e48d17c2a692838c2b5228f3fbafe8fcbeebbf39bf2cc75fb42b947e0596]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```
 
 
 
 You have just created your infrastructure! You can see your newly created resources by
  navigating to
  [http://localhost/](http://localhost/).

## Destroy the infrastructure

Terraform offers a convenient way to destroy existing infrastructure resources. 

If you would like to delete the infrastructure, run the `terraform destroy` command shown below. Terraform will display
 the
 resources it plans to clean up. 
```shell
$ terraform destroy
docker_image.nginx: Refreshing state... [id=sha256:c919045c4c2b0b0007c606e763ed2c830c7b1d038ce878a3c0d6f5b81e6ab80bnginx:latest]
docker_container.nginx: Refreshing state... [id=44a2e48d17c2a692838c2b5228f3fbafe8fcbeebbf39bf2cc75fb42b947e0596]

Terraform used the selected providers to generate the following execution plan. Resource actions are
indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # docker_container.nginx will be destroyed
  - resource "docker_container" "nginx" {
      - attach            = false -> null
      - command           = [
          - "nginx",
          - "-g",
          - "daemon off;",
        ] -> null
      - cpu_shares        = 0 -> null
      - dns               = [] -> null
      - dns_opts          = [] -> null
      - dns_search        = [] -> null
      - entrypoint        = [
          - "/docker-entrypoint.sh",
        ] -> null
      - env               = [] -> null
      - gateway           = "172.17.0.1" -> null
      - group_add         = [] -> null
      - hostname          = "44a2e48d17c2" -> null
      - id                = "44a2e48d17c2a692838c2b5228f3fbafe8fcbeebbf39bf2cc75fb42b947e0596" -> null
      - image             = "sha256:c919045c4c2b0b0007c606e763ed2c830c7b1d038ce878a3c0d6f5b81e6ab80b" -> null
      - init              = false -> null
      - ip_address        = "172.17.0.2" -> null
      - ip_prefix_length  = 16 -> null
      - ipc_mode          = "private" -> null
      - links             = [] -> null
      - log_driver        = "json-file" -> null
      - log_opts          = {} -> null
      - logs              = false -> null
      - max_retry_count   = 0 -> null
      - memory            = 0 -> null
      - memory_swap       = 0 -> null
      - must_run          = true -> null
      - name              = "training" -> null
      - network_data      = [
          - {
              - gateway                   = "172.17.0.1"
              - global_ipv6_address       = ""
              - global_ipv6_prefix_length = 0
              - ip_address                = "172.17.0.2"
              - ip_prefix_length          = 16
              - ipv6_gateway              = ""
              - network_name              = "bridge"
            },
        ] -> null
      - network_mode      = "default" -> null
      - privileged        = false -> null
      - publish_all_ports = false -> null
      - read_only         = false -> null
      - remove_volumes    = true -> null
      - restart           = "no" -> null
      - rm                = false -> null
      - security_opts     = [] -> null
      - shm_size          = 64 -> null
      - start             = true -> null
      - stdin_open        = false -> null
      - storage_opts      = {} -> null
      - sysctls           = {} -> null
      - tmpfs             = {} -> null
      - tty               = false -> null

      - ports {
          - external = 80 -> null
          - internal = 80 -> null
          - ip       = "0.0.0.0" -> null
          - protocol = "tcp" -> null
        }
    }

  # docker_image.nginx will be destroyed
  - resource "docker_image" "nginx" {
      - id          = "sha256:c919045c4c2b0b0007c606e763ed2c830c7b1d038ce878a3c0d6f5b81e6ab80bnginx:latest" -> null
      - latest      = "sha256:c919045c4c2b0b0007c606e763ed2c830c7b1d038ce878a3c0d6f5b81e6ab80b" -> null
      - name        = "nginx:latest" -> null
      - repo_digest = "nginx@sha256:1c13bc6de5dfca749c377974146ac05256791ca2fe1979fc8e8278bf0121d285" -> null
    }

Plan: 0 to add, 0 to change, 2 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: 
```
Like the previous command, type `yes` to apply the proposed plan and hit ENTER. 
 ```shell
  Enter a value: yes

docker_container.nginx: Destroying... [id=44a2e48d17c2a692838c2b5228f3fbafe8fcbeebbf39bf2cc75fb42b947e0596]
docker_container.nginx: Destruction complete after 0s
docker_image.nginx: Destroying... [id=sha256:c919045c4c2b0b0007c606e763ed2c830c7b1d038ce878a3c0d6f5b81e6ab80bnginx:latest]
docker_image.nginx: Destruction complete after 0s

Destroy complete! Resources: 2 destroyed.
```
Terraform will destroy the resources it had created earlier.

## Next steps

You have now used Terraform to create and destroy infrastructure. Your configuration used `Docker` as a provider to create a web server resource.

For a more in-depth view on Terraform and its capabilities:

* Review the [Provider Documentation](https://www.terraform.io/docs/providers/index.html) to learn more about Terraform
's many different plugins. 

* Follow the [CLI tutorials](https://learn.hashicorp.com/collections/terraform/cli) to discover other useful commands. 

* Learn more configuration file customization using [Terraform's configuration language](https://learn.hashicorp.com/collections/terraform/configuration-language).
