---
title: Transferir ferramentas do Azure Stack a partir do GitHub | Documentos da Microsoft
description: Saiba como descarregar as ferramentas necessárias para trabalhar com o Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 9eb8b3aefedb3e3330715408595a21596f33a413
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58480944"
---
# <a name="download-azure-stack-tools-from-github"></a>Transferir ferramentas do Azure Stack a partir do GitHub

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

**Ferramentas de AzureStack** é um [repositório do GitHub](https://github.com/Azure/AzureStack-Tools) que aloja os módulos do PowerShell para gerir e implementar recursos no Azure Stack. Se estiver a planear estabelecer a conectividade VPN, pode baixar esses módulos do PowerShell para o Development Kit do Azure Stack ou para um cliente externo com base em Windows. Para obter essas ferramentas, clone o repositório do GitHub ou transfira o **AzureStack ferramentas** pasta ao executar o seguinte script:

```powershell
# Change directory to the root directory. 
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```

## <a name="functionality-provided-by-the-modules"></a>Funcionalidade fornecida pelos módulos

O **AzureStack ferramentas** repositório contém módulos do PowerShell que suportam as seguintes funcionalidades para o Azure Stack:  

| Funcionalidade | Descrição | Quem pode utilizar este módulo? |
| --- | --- | --- |
| [Recursos de nuvem](user/azure-stack-validate-templates.md) | Utilize este módulo para obter os recursos de nuvem de uma nuvem. Por exemplo, ao utilizar este módulo, pode obter os recursos de nuvem, como a versão de API e recursos do Azure Resource Manager. Também pode obter as extensões VM para o Azure Stack e clouds do Azure através deste módulo. | Operadores da nuvem e de utilizadores |
| [Política do Gestor de recursos para o Azure Stack](user/azure-stack-policy-module.md) | Utilize este módulo para configurar uma subscrição do Azure ou um grupo de recursos do Azure com a mesmo controle de versão e disponibilidade do serviço como o Azure Stack. | Operadores da nuvem e de utilizadores |
| [Registe-se com o Azure](azure-stack-register.md) | Utilize este módulo para registar a sua instância do kit de desenvolvimento com o Azure. Após o registro, pode transferir os itens do marketplace do Azure e utilizá-los no Azure Stack. | Operadores da nuvem |
| [Implementação de pilha do Azure](azure-stack-run-powershell-script.md) | Utilize este módulo para preparar o computador de anfitrião do Azure Stack de implantação e reimplantação utilizando a imagem de disco rígido virtual (VHD) do Azure Stack. | Operadores da nuvem|
| [Ligar ao Azure Stack](azure-stack-connect-powershell.md) | Utilize este módulo para configurar a conectividade VPN para o Azure Stack. | Operadores da nuvem e de utilizadores |
| [A validação do modelo](user/azure-stack-validate-templates.md) | Utilize este módulo para verificar se um existente ou um novo modelo pode ser implementado para o Azure Stack. | Operadores da nuvem e de utilizadores|


## <a name="next-steps"></a>Passos Seguintes
* [Configurar o ambiente do PowerShell do utilizador do Azure Stack](user/azure-stack-powershell-configure-user.md)   
* [Ligar ao Kit de desenvolvimento do Azure Stack através de uma VPN](azure-stack-connect-azure-stack.md)  
