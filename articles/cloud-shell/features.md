---
title: Recursos de Azure Cloud Shell | Microsoft Docs
description: Visão geral dos recursos no Azure Cloud Shell
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
ms.openlocfilehash: 1354f7befd8c38537a555e17733f431dd488cf60
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742059"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Recursos & ferramentas para Azure Cloud Shell

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Azure Cloud Shell é executado `Ubuntu 16.04 LTS`em.

## <a name="features"></a>Funcionalidades

### <a name="secure-automatic-authentication"></a>Autenticação automática segura

Cloud Shell autentica com segurança e automaticamente o acesso à conta para o CLI do Azure e Azure PowerShell.

### <a name="home-persistence-across-sessions"></a>$HOME persistência entre as sessões

Para manter os arquivos entre as sessões, Cloud Shell orienta você a anexar um compartilhamento de arquivos do Azure na primeira inicialização.
Depois de concluído, Cloud Shell anexará automaticamente seu armazenamento (montado `$HOME\clouddrive`como) para todas as sessões futuras.
Além disso, `$HOME` seu diretório é mantido como um. img em seu compartilhamento de arquivos do Azure.
Os arquivos fora `$HOME` do e o estado da máquina não são persistidos entre as sessões. Use as práticas recomendadas ao armazenar segredos, como chaves SSH. Serviços como [Azure Key Vault têm tutoriais para a instalação](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites).

[Saiba mais sobre como manter arquivos em Cloud Shell.](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Unidade do Azure (Azure:)

O PowerShell no Cloud Shell inicia você no Azure Drive`Azure:`().
A unidade do Azure permite a fácil descoberta e navegação de recursos do Azure, como computação, rede, armazenamento, etc., semelhante à navegação do sistema de arquivos.
Você pode continuar a usar os cmdlets de [Azure PowerShell](https://docs.microsoft.com/powershell/azure) familiares para gerenciar esses recursos, independentemente da unidade em que você estiver.
Todas as alterações feitas aos recursos do Azure, feitas diretamente em portal do Azure ou por meio de cmdlets Azure PowerShell, são refletidas na unidade do Azure.  Você pode executar `dir -Force` o para atualizar seus recursos.

![](media/features-powershell/azure-drive.png)

### <a name="manage-exchange-online"></a>Gerenciar o Exchange Online

O PowerShell no Cloud Shell contém uma compilação particular do módulo do Exchange Online.  Execute `Connect-EXOPSSession` para obter seus cmdlets do Exchange.

![](media/features-powershell/exchangeonline.png)

 Execute `Get-Command -Module tmp_*`
> [!NOTE]
> O nome do módulo deve começar `tmp_`com, se você tiver instalado módulos com o mesmo prefixo, seus cmdlets também serão exibidos. 

![](media/features-powershell/exchangeonlinecmdlets.png)

### <a name="deep-integration-with-open-source-tooling"></a>Integração profunda com ferramentas de código-fonte aberto

O Cloud Shell inclui autenticação pré-configurada para ferramentas de software livre, como Terraform, Ansible e chefe inspec. Experimente o exemplo de instruções.

## <a name="tools"></a>Ferramentas

|Category   |Nome   |
|---|---|
|Ferramentas do Linux            |raso<br> zsh<br> m<br> tmux<br> procurar<br>               |
|Ferramentas do Azure            |[CLI do Azure](https://github.com/Azure/azure-cli) e [CLI clássica do Azure](https://github.com/Azure/azure-xplat-cli)<br> [AZCopy](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#writing-your-first-azcopy-command)<br> [CLI do Azure Functions](https://github.com/Azure/azure-functions-core-tools)<br> [CLI do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard)<br> [blobxfer](https://github.com/Azure/blobxfer)|
|Editores de texto           |código (editor de Cloud Shell)<br> Vim<br> Nano<br> Emacs    |
|Controle do código-fonte         |git                    |
|Ferramentas de Build            |Make<br> Maven<br> npm<br> pontos         |
|Contentores             |[Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [CLI DO DC/SO](https://github.com/dcos/dcos-cli)         |
|Bases de Dados              |Cliente MySQL<br> PostgreSql client<br> [Utilitário sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Outros                  |Cliente iPython<br> [CLI do Cloud Foundry](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Chef InSpec](https://www.chef.io/inspec/)<br> [Puppet](https://puppet.com/docs/bolt/latest/bolt.html)<br> [HashiCorp Pack](https://www.packer.io/)|

## <a name="language-support"></a>Suporte de idiomas

|Idioma   |Version   |
|---|---|
|.NET Core  |2.0.0       |
|Ir Para         |1.9        |
|Java       |1.8        |
|Node.js    |8.9.4      |
|PowerShell |[6.2.0](https://github.com/PowerShell/powershell/releases)       |
|Python     |2,7 e 3,5 (padrão)|

## <a name="next-steps"></a>Passos Seguintes
[Bash no guia de início rápido Cloud Shell](quickstart.md) <br>
[PowerShell no guia de início rápido Cloud Shell](quickstart-powershell.md) <br>
[Saiba mais sobre o CLI do Azure](https://docs.microsoft.com/cli/azure/) <br>
[Saiba mais sobre o Azure PowerShell](https://docs.microsoft.com/powershell/azure/) <br>
