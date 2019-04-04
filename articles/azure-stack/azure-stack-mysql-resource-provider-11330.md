---
title: Azure notas de versão do fornecedor 1.1.30.0 de recursos do MySQL de pilha | Documentos da Microsoft
description: Saiba mais sobre o que há na atualização mais recente do Azure Stack MySQL recursos fornecedor, incluindo os problemas conhecidos e onde para baixá-lo.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: e0101aebadcaef71f35c72b54f9126e69cff0f61
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58882840"
---
# <a name="mysql-resource-provider-11330--release-notes"></a>Notas de versão do fornecedor 1.1.33.0 de recursos do MySQL

*Aplica-se a Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Estas notas de versão descrevem as melhorias e problemas conhecidos no MySQL 1.1.33.0 de versão do fornecedor de recursos.

## <a name="build-reference"></a>Criar a referência
Transferir o fornecedor de recursos do MySQL, binário e, em seguida, execute o Self-extractor para extrair o conteúdo para um diretório temporário. O fornecedor de recursos tem um mínimo correspondente do Azure Stack criar. A versão de lançamento do Azure Stack mínima necessária para instalar esta versão do fornecedor de recursos MySQL está listada abaixo:

> |Versão mínima do Azure Stack|Versão do fornecedor de recursos de MySQL|
> |-----|-----|
> |Versão 1808 (1.1808.0.97)|[MySQL RP versão 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Aplicar a atualização com suporte do Azure Stack mínimo ao seu sistema integrado do Azure Stack ou implementar a mais recente do Azure Stack Development Kit (ASDK) antes de implementar a versão mais recente do fornecedor de recursos MySQL.

## <a name="new-features-and-fixes"></a>Novas funcionalidades e correções
Esta versão do fornecedor de recursos do MySQL do Azure Stack inclui as seguintes melhorias e correções:

### <a name="fixes"></a>Correções
- **Extensão de portal do fornecedor de recursos do MySQL, pode escolher a subscrição errada**. O fornecedor de recursos do MySQL utiliza chamadas do Azure Resource Manager para determinar a primeira assinatura de administrador de serviço para utilizar, que podem não ser o *subscrição do fornecedor predefinido*. Se isto acontecer, o fornecedor de recursos do MySQL não funciona normalmente. 

- **MySQL server de alojamento não listar alojado bases de dados.** Bases de dados criada por utilizadores não poderão ser listadas ao visualizar os recursos de inquilino para alojar servidores do MySQL.

- **Implementação de fornecedor (1.1.30.0) de recursos MySQL anterior poderá falhar se não estiver ativada TLS 1.2**. Atualizar o fornecedor de recursos do MySQL 1.1.33.0 para ativar o TLS 1.2 ao implementar o fornecedor de recursos, a atualizar o fornecedor de recursos ou girando segredos. 

- **Falha de rotação secreta do fornecedor de recursos MySQL**. Foi corrigido o problema resulta no seguinte código de erro quando alternar segredos:
`New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Problemas conhecidos 

- **MySQL SKUs pode demorar até uma hora para ser visível no portal**. Pode demorar até uma hora para recentemente SKUs criados para ser visível para utilização durante a criação de novas bases de dados MySQL. 

    **Solução**: Nenhum.

- **Reutilizado inícios de sessão do MySQL**. Tentar criar um novo MySQL o início de sessão com o mesmo nome de utilizador como um início de sessão existente na mesma subscrição resultará em reutilizar o mesmo início de sessão e a palavra-passe existente. 

    **Solução**: Utilize nomes de utilizador diferente durante a criação de novos inícios de sessão na mesma subscrição ou criar inícios de sessão com o mesmo nome de utilizador em subscrições diferentes.

- **Inícios de sessão de MySQL partilhados inconsistências nos dados**. Se um início de sessão do MySQL é partilhado por várias bases de dados do MySQL na mesma subscrição, alterar a palavra-passe de início de sessão irá provocar inconsistências nos dados.

    **Solução**: Utilize sempre os inícios de sessão diferentes para bases de dados diferentes na mesma subscrição.


### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Problemas conhecidos para os administradores de nuvem operacionais do Azure Stack
Consulte a documentação sobre o [notas de versão do Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Passos Seguintes
[Saiba mais sobre o fornecedor de recursos do MySQL](azure-stack-mysql-resource-provider.md).

[Preparar para implementar o fornecedor de recursos do MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Atualizar o fornecedor de recursos do MySQL a partir de uma versão anterior](azure-stack-mysql-resource-provider-update.md). 