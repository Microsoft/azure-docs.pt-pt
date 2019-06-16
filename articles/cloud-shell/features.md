---
title: As funcionalidades do Azure Cloud Shell | Documentos da Microsoft
description: Descrição geral das funcionalidades de Bash no Azure Cloud Shell
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
ms.openlocfilehash: 6b5f0e96b90ee0515c0a86f41c6ee2161d6c54a6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66752711"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Funcionalidades e ferramentas para o Azure Cloud Shell

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

O Azure Cloud Shell é executado no `Ubuntu 16.04 LTS`.

## <a name="features"></a>Funcionalidades

### <a name="secure-automatic-authentication"></a>Autenticação automática segura

Cloud Shell com segurança e automaticamente autentica o acesso de conta para a CLI do Azure e o Azure PowerShell.

### <a name="home-persistence-across-sessions"></a>Persistência $HOME entre sessões

Para manter os ficheiros em sessões, Cloud Shell explica como anexar uma partilha de ficheiros do Azure na primeira execução.
Depois de concluído, o Cloud Shell ligará automaticamente o armazenamento (montado como `$HOME\clouddrive`) para todas as futuras sessões.
Além disso, seu `$HOME` diretório é mantido como um. img na partilha de ficheiros do Azure.
Ficheiros fora do `$HOME` e o estado da máquina não são mantidas entre sessões. Utilize as melhores práticas ao armazenar segredos, como chaves SSH. Serviços, como [do Azure Key Vault tem tutoriais para a configuração](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites).

[Saiba mais sobre ficheiros persistentes no Cloud Shell.](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Unidade Azure (Azure:)

PowerShell no Cloud Shell é iniciado o na unidade do Azure (`Azure:`).
A unidade do Azure permite que o processo de deteção e a navegação de recursos do Azure, como computação, rede, etc. do armazenamento semelhante à navegação de sistema de ficheiros.
Pode continuar a utilizar o familiar [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure) gerir estes recursos, independentemente da unidade está no.
Todas as alterações efetuadas aos recursos do Azure, seja feito diretamente no portal do Azure ou através de cmdlets do PowerShell do Azure, são refletidas na unidade do Azure.  Pode executar `dir -Force` para atualizar os seus recursos.

![](media/features-powershell/azure-drive.png)

### <a name="manage-exchange-online"></a>Gira o Exchange Online

PowerShell no Cloud Shell contém uma compilação privada do módulo do Exchange Online.  Executar `Connect-EXOPSSession` para obter os cmdlets do Exchange.

![](media/features-powershell/exchangeonline.png)

 Execute `Get-Command -Module tmp_*`
> [!NOTE]
> O nome do módulo deve iniciar com `tmp_`, se tiver instalado módulos com o mesmo prefixo, seus cmdlets também será apresentada. 

![](media/features-powershell/exchangeonlinecmdlets.png)

### <a name="deep-integration-with-open-source-tooling"></a>Uma profunda integração com ferramentas de código aberto

O cloud Shell inclui autenticação previamente configurada para ferramentas de código-fonte aberto, como o Terraform, o Ansible e Chef InSpec. Faça um teste das instruções de exemplo.

## <a name="tools"></a>Ferramentas

|Category   |Name   |
|---|---|
|Ferramentas do Linux            |Bash<br> zsh<br> sh<br> tmux<br> aprofundar<br>               |
|Ferramentas do Azure            |[CLI do Azure](https://github.com/Azure/azure-cli) e [CLI clássica do Azure](https://github.com/Azure/azure-xplat-cli)<br> [AZCopy](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#writing-your-first-azcopy-command)<br> [CLI do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard)<br> [blobxfer](https://github.com/Azure/blobxfer)|
|Editores de texto           |Código (editor de Cloud Shell)<br> vim<br> nano<br> emacs    |
|Controlo de origem         |git                    |
|Ferramentas de compilação            |Tornar<br> Maven<br> npm<br> pip         |
|Contentores             |[Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [DC/OS CLI](https://github.com/dcos/dcos-cli)         |
|Bases de Dados              |Cliente de MySQL<br> PostgreSql client<br> [Utilitário de SQLCMD](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Outros                  |iPython Client<br> [Cloud Foundry da CLI](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Chef InSpec](https://www.chef.io/inspec/)|

## <a name="language-support"></a>Suporte de idiomas

|Idioma   |Version   |
|---|---|
|.NET Core  |2.0.0       |
|Ir         |1.9        |
|Java       |1.8        |
|Node.js    |8.9.4      |
|PowerShell |[6.2.0](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 e 3.5 (predefinição)|

## <a name="next-steps"></a>Passos Seguintes
[Guia de introdução do Cloud Shell de bash](quickstart.md) <br>
[PowerShell no guia de introdução do Cloud Shell](quickstart-powershell.md) <br>
[Saiba mais sobre a CLI do Azure](https://docs.microsoft.com/cli/azure/) <br>
[Saiba mais sobre o Azure PowerShell](https://docs.microsoft.com/powershell/azure/) <br>
