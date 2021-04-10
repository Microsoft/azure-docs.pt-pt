---
title: Azure Cloud Shell apresenta | Microsoft Docs
description: Visão geral das funcionalidades em Azure Cloud Shell
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: damaerte
ms.openlocfilehash: c707a6522d037ba3926b55579e501eadd2764416
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104798277"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Possui ferramentas & para Azure Cloud Shell

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

A Azure Cloud Shell `Ubuntu 16.04 LTS` funciona.

## <a name="features"></a>Funcionalidades

### <a name="secure-automatic-authentication"></a>Garantir a autenticação automática

A Cloud Shell autentica de forma segura e automática o acesso à conta para o Azure CLI e para o Azure PowerShell.

### <a name="home-persistence-across-sessions"></a>$HOME persistência em todas as sessões

Para persistir ficheiros em todas as sessões, a Cloud Shell acompanha-o através da anexação de uma partilha de ficheiros Azure no primeiro lançamento.
Uma vez concluída, a Cloud Shell anexará automaticamente o seu armazenamento (montado `$HOME\clouddrive` como) para todas as sessões futuras.
Além disso, o seu `$HOME` diretório é persistido como um .img na sua parte do Ficheiro Azure.
Os ficheiros fora e o estado da `$HOME` máquina não são persistidos em todas as sessões. Utilize as melhores práticas ao armazenar segredos como as chaves SSH. Serviços como [o Azure Key Vault têm tutoriais para configuração.](../key-vault/general/manage-with-cli2.md#prerequisites)

[Saiba mais sobre os ficheiros persistentes na Cloud Shell.](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Unidade azul (Azure:)

PowerShell in Cloud Shell fornece a unidade Azure `Azure:` (). Pode mudar para a unidade Azure com `cd Azure:` e voltar para o seu diretório de casa com `cd  ~` .
A unidade Azure permite uma fácil descoberta e navegação de recursos Azure tais como Computação, Rede, Armazenamento, etc. semelhante à navegação do sistema de ficheiros.
Pode continuar a utilizar os [conhecidos cmdlets Azure PowerShell](/powershell/azure) para gerir estes recursos, independentemente da unidade em que se encontre.
Quaisquer alterações feitas aos recursos Azure, efetuadas diretamente no portal Azure ou através de cmdlets Azure PowerShell, refletem-se na unidade Azure.  Pode correr `dir -Force` para refrescar os seus recursos.

![Screenshot de um Azure Cloud Shell sendo inicializado e uma lista de recursos de diretório.](media/features-powershell/azure-drive.png)

### <a name="manage-exchange-online"></a>Gerir Troca Online

PowerShell em Cloud Shell contém uma construção privada do módulo Exchange Online.  Corra `Connect-EXOPSSession` para obter os seus cmdlets de troca.

![Screenshot de uma Azure Cloud Shell executando os comandos Connect-EXOPSSession e Get-User.](media/features-powershell/exchangeonline.png)

 Executar `Get-Command -Module tmp_*`
> [!NOTE]
> O nome do módulo deve começar `tmp_` por, se tiver módulos instalados com o mesmo prefixo, os seus cmdlets também serão lançados. 

![Screenshot de uma Azure Cloud Shell executando o comando Get-Command -Módulo tmp_*.](media/features-powershell/exchangeonlinecmdlets.png)

### <a name="deep-integration-with-open-source-tooling"></a>Integração profunda com ferramentas de código aberto

A Cloud Shell inclui a autenticação pré-configurada para ferramentas de código aberto como Terraform, Ansible e Chef InSpec. Experimente a partir dos passos de exemplo.

## <a name="tools"></a>Ferramentas

|Categoria   |Name   |
|---|---|
|Ferramentas Linux            |bash<br> zsh<br> sh<br> tmux<br> cavar<br>               |
|Ferramentas do Azure            |[CLI clássico de Azure E](https://github.com/Azure/azure-cli) [Azure](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](../storage/common/storage-use-azcopy-v10.md)<br> [CLI das Funções do Azure](https://github.com/Azure/azure-functions-core-tools)<br> [CLI do Service Fabric](../service-fabric/service-fabric-cli.md)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard)<br> [blobxfer](https://github.com/Azure/blobxfer)|
|Editores de texto           |código (editor cloud shell)<br> vim<br> nano<br> emacs    |
|Controlo de código fonte         |git                    |
|Ferramentas de compilação            |make<br> maven<br> npm<br> pip         |
|Contentores             |[Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [DC/OS CLI](https://github.com/dcos/dcos-cli)         |
|Bases de Dados              |Cliente MySQL<br> Cliente PostgreSql<br> [sqlcmd Utility](/sql/tools/sqlcmd-utility) (Utilitário sqlcmd)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Outro                  |Cliente iPython<br> [Cloud Foundry CLI](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Chef InSpec](https://www.chef.io/inspec/)<br> [Parafuso de marioneta](https://puppet.com/docs/bolt/latest/bolt.html)<br> [HashiCorp Packer](https://www.packer.io/)<br> [Escritório 365 CLI](https://pnp.github.io/office365-cli/)|

## <a name="language-support"></a>Suporte de idiomas

|Linguagem   |Versão   |
|---|---|
|.NET Core  |[3.1.302](https://github.com/dotnet/core/blob/master/release-notes/3.1/3.1.6/3.1.302-download.md)       |
|Go         |1.9        |
|Java       |1.8        |
|Node.js    |8.16.0      |
|PowerShell |[7.0.0](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 e 3.7 (padrão)|

## <a name="next-steps"></a>Passos seguintes
[Bash em Cloud Shell Quickstart](quickstart.md) <br>
[PowerShell em Cloud Shell Quickstart](quickstart-powershell.md) <br>
[Saiba mais sobre a Azure CLI](/cli/azure/) <br>
[Saiba mais sobre a Azure PowerShell](/powershell/azure/) <br>
