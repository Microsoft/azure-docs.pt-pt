---
title: Crie um canal Conda personalizado para gestão de pacotes
description: Saiba como criar um canal Conda personalizado para gestão de pacotes
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 528ba4a1be3650a81772d78a438f03611b9bd761
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102107946"
---
# <a name="create-a-custom-conda-channel-for-package-management"></a>Crie um canal Conda personalizado para gestão de pacotes 
Ao instalar pacotes Python, o gestor de pacotes Conda utiliza canais para procurar pacotes. Poderá ser necessário criar um canal Conda personalizado por várias razões. Por exemplo, pode descobrir que:

- o seu espaço de trabalho é a exfiltração de dados protegida e as ligações de saída estão bloqueadas.  
- tem pacotes que não quer enviar para repositórios públicos.
- pretende configurar o repositório alternativo para os utilizadores dentro do seu espaço de trabalho.

Neste artigo, forneceremos um guia passo a passo para ajudá-lo a criar o seu canal Conda personalizado dentro da sua conta de Armazenamento de Data Lake Azure.

## <a name="set-up-your-local-machine"></a>Configurar a sua máquina local

1. Instale a Conda na sua máquina local. Pode consultar o tempo de funcionamento da [Faísca Azure Synapse](./apache-spark-version-support.md) para identificar a versão Conda que é usada no mesmo tempo de funcionamento.
   
2. Para criar um canal personalizado, instale conda-build.
```
conda install conda-build
```
3. Organize todos os pacotes para a plataforma que deseja servir. Neste exemplo, instalaremos o arquivo Anaconda na sua máquina local.

```
sudo wget https://repo.continuum.io/archive/Anaconda3-4.4.0-Linux-x86_64.sh 
sudo chmod +x Anaconda3-4.4.0-Linux-x86_64.sh  
sudo bash Anaconda3-4.4.0-Linux-x86_64.sh -b -p /usr/lib/anaconda3 
export PATH="/usr/lib/anaconda3/bin:$PATH" 
sudo chmod 777 -R /usr/lib/anaconda3a.  
```
## <a name="mount-the-storage-account-onto-your-machine"></a>Monte a conta de armazenamento na sua máquina
Em seguida, vamos montar a conta Azure Data Lake Storage Gen2 na sua máquina local. Este processo também pode ser feito com uma conta WASB; no entanto, vamos passar por um exemplo para a conta ADLSg2 
 
Para mais informações sobre como montar a conta de armazenamento na sua máquina local, pode visitar [esta página.](https://github.com/Azure/azure-storage-fuse#blobfuse ) 

1. Pode instalar blobfuse a partir do Repositório de Software Linux para produtos Microsoft.

```
wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb 
sudo dpkg -i packages-microsoft-prod.deb 
sudo apt-get update 
sudo apt-get install blobfuse fuse 
export AZURE_STORAGE_ACCOUNT=<<myaccountname>
export AZURE_STORAGE_ACCESS_KEY=<<myaccountkey>>
export AZURE_STORAGE_BLOB_ENDPOINT=*.dfs.core.windows.net 
```

2. Crie o seu ponto de montagem ```mkdir /path/to/mount``` () e monte um recipiente Blob com blobfuse. Neste exemplo, vamos usar o canal **privado** de valor para a variável **mycontainer.**
   
```
blobfuse /path/to/mount --container-name=mycontainer --tmp-path=/mnt/blobfusetmp --use-adls=true --log-level=LOG_DEBUG 
sudo mkdir -p /mnt/blobfusetmp
sudo chown <myuser> /mnt/blobfusetmp
```
## <a name="create-the-channel"></a>Criar o canal
No próximo conjunto de passos, criaremos um canal Conda personalizado. 

1. Na sua máquina local, crie um diretório para organizar todos os pacotes para o seu canal personalizado.
   
```
mkdir /home/trusted-service-user/privatechannel 
cd ~/privatechannel/ 
mkdir channel1/linux64 
```

2. Organize todos os ```tar.bz2``` pacotes https://repo.anaconda.com/pkgs/main/linux-64/ para a subdiretória. Certifique-se de incluir também todos os pacotes dependentes de tar.bz2. 

```
cd channel1 
mkdir noarch 
echo '{}' > noarch/repodata.json 
bzip2 -k noarch/repodata.json 

// Create channel 

conda index channel1/noarch 
conda index channel1/linux-64 
conda index channel1 
```

Para mais informações, também pode [visitar o guia do utilizador da Conda](https://docs.conda.io/projects/conda/latest/user-guide/tasks/create-custom-channels.html) para criar canais personalizados. 

## <a name="storage-account-permissions"></a>Permissões de conta de armazenamento
Agora, precisamos validar as permissões na conta de armazenamento. Para definir estas permissões, navegue para o caminho onde será criado o canal personalizado. Em seguida, crie um símbolo SAS para ```privatechannel``` que tenha lido, listado e execute permissões. 

O nome do canal será agora o URL BLOB SAS que é gerado a partir deste processo.  

## <a name="create-a-sample-conda-environment-configuration-file"></a>Criar um ficheiro de configuração ambiente Conda de amostra
Por último, verifique o processo de instalação criando um ficheiro Conda de ```environment.yml``` amostra. Se tiver num espaço de trabalho habilitado para o DEP, tem de especificar o ``nodefaults`` canal no seu ficheiro ambiental.

Aqui está um ficheiro de configuração conda exemplo:
```
name: sample 
channels: 
  - https://<<storage account name>>.blob.core.windows.net/privatechannel/channel?<<SAS Token>
  - nodefaults 
dependencies: 
  - openssl 
  - ncurses 
```
Uma vez criado o ficheiro Conda da amostra, pode criar um ambiente Conda virtual. 

```
conda env create –file sample.yml  
source activate env 
conda list 
```
Agora que verificou o seu canal personalizado, pode usar o processo [de gestão](./apache-spark-manage-python-packages.md) da piscina Python para atualizar as bibliotecas da sua piscina Apache Spark.

## <a name="next-steps"></a>Passos seguintes
- Ver as bibliotecas padrão: [Suporte à versão Apache Spark](apache-spark-version-support.md)
- Gerir pacotes Python: [Gestão de pacotes Python](./apache-spark-manage-python-packages.md)

