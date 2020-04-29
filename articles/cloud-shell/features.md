---
title: Características da Casca de Nuvem Azure / Microsoft Docs
description: Visão geral das características em Azure Cloud Shell
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
ms.openlocfilehash: 78275211b77cb34cf9aa92e44778e5d4d81fffed
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80521019"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Funcionalidades & ferramentas para Casca de Nuvem Azure

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Azure Cloud Shell `Ubuntu 16.04 LTS`corre em .

## <a name="features"></a>Funcionalidades

### <a name="secure-automatic-authentication"></a>Autenticação automática segura

Cloud Shell autentica de forma segura e automática o acesso à conta para o Azure CLI e O PowerShell Azure.

### <a name="home-persistence-across-sessions"></a>$HOME persistência em sessões

Para persistir ficheiros em sessões, a Cloud Shell acompanha-o através da anexação de uma partilha de ficheiros Azure no primeiro lançamento.
Uma vez concluída, a Cloud Shell anexará `$HOME\clouddrive`automaticamente o seu armazenamento (montado como) para todas as sessões futuras.
Além disso, o seu `$HOME` diretório é persistido como uma .img na sua partilha de Ficheiros Azure.
Os ficheiros fora do estado da `$HOME` máquina não são persistidos em sessões. Utilize as melhores práticas ao armazenar segredos como chaves SSH. Serviços como [o Azure Key Vault têm tutoriais para configuração.](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites)

[Saiba mais sobre ficheiros persistentes na Cloud Shell.](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Unidade Azure (Azure:)

PowerShell em Cloud Shell fornece a`Azure:`unidade Azure ( ). Pode mudar para a unidade `cd Azure:` Azure com e `cd  ~`voltar para o seu diretório de casa com .
A unidade Azure permite uma fácil descoberta e navegação de recursos Azure, tais como Compute, Network, Storage etc. similar esquelético sinuoso à navegação do sistema de ficheiros.
Pode continuar a utilizar os [conhecidos cmdlets Azure PowerShell](https://docs.microsoft.com/powershell/azure) para gerir estes recursos, independentemente da unidade em que se encontram.
Quaisquer alterações feitas aos recursos Azure, quer feitas diretamente no portal Azure, quer através de cmdlets Azure PowerShell, refletem-se na unidade Azure.  Pode correr `dir -Force` para refrescar os seus recursos.

![](media/features-powershell/azure-drive.png)

### <a name="manage-exchange-online"></a>Gerir o Exchange Online

PowerShell in Cloud Shell contém uma construção privada do módulo Exchange Online.  Corra `Connect-EXOPSSession` para obter os seus cmdlets de troca.

![](media/features-powershell/exchangeonline.png)

 Execute `Get-Command -Module tmp_*`
> [!NOTE]
> O nome do `tmp_`módulo deve começar com, se tiver instalado módulos com o mesmo prefixo, os seus cmdlets também serão emergidos. 

![](media/features-powershell/exchangeonlinecmdlets.png)

### <a name="deep-integration-with-open-source-tooling"></a>Integração profunda com ferramentas de código aberto

Cloud Shell inclui autenticação pré-configurada para ferramentas de código aberto como Terraform, Ansible e Chef InSpec. Experimente a partir dos passos de exemplo.

## <a name="tools"></a>Ferramentas

|Categoria   |Nome   |
|---|---|
|Ferramentas Linux            |bash<br> zsh<br> sh<br> tmux<br> cavar<br>               |
|Ferramentas Azure            |[ClI clássico azure cli](https://github.com/Azure/azure-cli) e [Azure](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#writing-your-first-azcopy-command)<br> [CLI das Funções do Azure](https://github.com/Azure/azure-functions-core-tools)<br> [CLI do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard)<br> [blobxfer](https://github.com/Azure/blobxfer)|
|Editores de texto           |código (editor da Cloud Shell)<br> vim<br> nano<br> emacs    |
|Controlo de código fonte         |git                    |
|Ferramentas de compilação            |modelo<br> maven<br> npm<br> pip         |
|Contentores             |[Docker Machine](https://github.com/docker/machine)<br> [Rio Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Leme](https://github.com/kubernetes/helm)<br> [DC/OS CLI](https://github.com/dcos/dcos-cli)         |
|Bases de Dados              |Cliente MySQL<br> Cliente PostgreSql<br> [utilidade sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Outros                  |iPython Cliente<br> [Cloud Foundry CLI](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Chef InSpec](https://www.chef.io/inspec/)<br> [Parafuso de marioneta](https://puppet.com/docs/bolt/latest/bolt.html)<br> [HashiCorp Packer](https://www.packer.io/)|

## <a name="language-support"></a>Suporte de idiomas

|Idioma   |Versão   |
|---|---|
|.NET Core  |2.2.402       |
|Ir         |1.9        |
|Java       |1.8        |
|Node.js    |8.16.0      |
|PowerShell |[7.0.0](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 e 3.5 (padrão)|

## <a name="next-steps"></a>Passos seguintes
[Bash em Cloud Shell Quickstart](quickstart.md) <br>
[PowerShell em Cloud Shell Quickstart](quickstart-powershell.md) <br>
[Conheça o Azure CLI](https://docs.microsoft.com/cli/azure/) <br>
[Saiba mais sobre o Azure PowerShell](https://docs.microsoft.com/powershell/azure/) <br>
