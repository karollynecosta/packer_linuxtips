# Descomplicando o Packer
Repository to annotations about Packer from Hashicorp by LinuxTips

[Site Packer](https://www.packer.io/)
[JSON website](https://www.json.org/json-pt.html)

# Indice de estudos
1. [Packer Terminology](#Terminology)
2. [Estrutura de um template](#EstruturaTemplate)
3. [Comandos](#Comandos)

## Packer Terminology <a name="Terminology"></a>

Artifacts: é a saída de um build
```
Are the results of a single build, and are usually a set of IDs or files to represent a machine image. Every builder produces a single artifact. As an example, in the case of the Amazon EC2 builder, the artifact is a set of AMI IDs (one per region). For the VMware builder, the artifact is a directory of files comprising the created virtual machine.
```

Builds: uma tarefa que roda e produz uma imagem, podendo ser multiplos
```
are a single task that eventually produces an image for a single platform. Multiple builds run in parallel. Example usage in a sentence: "The Packer build produced an AMI to run our web application." Or: "Packer is running the builds now for VMware, AWS, and VirtualBox."
```

Builders: parte do arquivo que especifica como vai ser o build
```
are components of Packer that are able to create a machine image for a single platform. Builders read in some configuration and use that to run and generate a machine image. A builder is invoked as part of a build in order to create the actual resulting images. Example builders include VirtualBox, VMware, and Amazon EC2.
```

Commands:
```
are sub-commands for the packer program that perform some job. An example command is "build", which is invoked as packer build. Packer ships with a set of commands out of the box in order to define its command-line interface.
```

Data Sources:
```
are components of Packer that fetch data from outside Packer and make it available to use within the template. Example of data sources include Amazon AMI, and Amazon Secrets Manager.
```

Post-processors:
```
are components of Packer that take the result of a builder or another post-processor and process that to create a new artifact. Examples of post-processors are compress to compress artifacts, upload to upload artifacts, etc.
```

Provisioners:
```
are components of Packer that install and configure software within a running machine prior to that machine being turned into a static image. They perform the major work of making the image contain useful software. Example provisioners include shell scripts, Chef, Puppet, etc.
```

Templates: arquivo descritivo para o binario packer ler e criar
```
are JSON files which define one or more builds by configuring the various components of Packer. Packer is able to read a template and use that information to create multiple machine images in parallel.
```

## Estrutura do Template <a name="EstruturaTemplate"></a>

The available keys within a template are listed below. Along with each key, it is noted whether it is required or not.

builders (REQUIRED):
```
is an array of one or more objects that defines the builders that will be used to create machine images for this template, and configures each of those builders. For more information on how to define and configure a builder, read the sub-section on configuring builders in templates.

Melhores práticas:
    - nao ter um ami-id hardcoded, utilizar source_ami_filter com owner confiável, sendo assim não terá impacto em uma mudanca de region, alem de utilizar a mais recente. Pulando assim, problemas de seguranca.
    - utilizar TAG
```

description (optional):
```
is a string providing a description of what the template does. This output is used only in the inspect command.
```

min_packer_version (optional):
```
is a string that has a minimum Packer version that is required to parse the template. This can be used to ensure that proper versions of Packer are used with the template. A max version can't be specified because Packer retains backwards compatibility with packer fix.
```

post-processors (optional):
```
is an array of one or more objects that defines the various post-processing steps to take with the built images. If not specified, then no post-processing will be done.  Post-processors are optional, and they can be used to upload artifacts, re-package, or more.
```

provisioners (optional):
```
is an array of one or more objects that defines the provisioners that will be used to install and configure software for the machines created by each of the builders. If it is not specified, then no provisioners will be run. For more information on how to define and configure a provisioner, read the sub-section on configuring provisioners in templates.
```

variables (optional):
```
is an object of one or more key/value strings that defines user variables contained in the template. If it is not specified, then no variables are defined. For more information on how to define and use user variables, read the sub-section on user variables in templates.

Pode ser utilizado um bloco variables:
{
  "variables": {
    "my_secret": "{{ vault `/secret/data/hello` `foo`}}"
  }
}

https://www.packer.io/docs/templates/legacy_json_templates/user-variables
```

comments (optional):
```
JSON doesn't support comments and Packer reports unknown keys as validation errors. If you'd like to comment your template, you can prefix a root level key with an underscore. Example:

{
  "_comment": "This is a comment",
  "builders": [{}]
}

Note:
    Packer supports HCL2 from version 1.6.0. The Hashicorp Configuration Language does support comments anywhere in template files.
    If comments are important to you, consider upgrading your JSON template to HCL2 using the packer hcl2_upgrade command.

```

## Comandos <a name="Comandos"></a>

Para instalar o packer com Docker:
```
docker run -it -v $PWD:/app -w /app --entrypoint "" hashicorp/packer:light sh

Legenda: $PWD:/app, copia o que esta neste projeto para dentro de /app sem a necessidade de ficar reiniciando o container
        -w workdir, onde o container ira iniciar
        -- entrypoint "" remove o entrypoint original da img, no caso inicia somente o sh
        --it torna acessivel o container via terminal
```

Para ter autocomplete:
```
packer -autocomplete-install
```

Troubleshooting:
```
packer build -debug
PACKER_LOG=1
-on-error=ask
```