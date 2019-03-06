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
ms.date: 12/10/2018
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: 5934d075378df9f04130b79eb43131d71eaa25af
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57449466"
---
# <a name="mysql-resource-provider-11300--release-notes"></a>Notas de versão do fornecedor 1.1.30.0 de recursos do MySQL

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Estas notas de versão descrevem as melhorias e problemas conhecidos no MySQL 1.1.30.0 de versão do fornecedor de recursos.

## <a name="build-reference"></a>Criar a referência
Transferir o fornecedor de recursos do MySQL, binário e, em seguida, execute o Self-extractor para extrair o conteúdo para um diretório temporário. O fornecedor de recursos tem um mínimo correspondente do Azure Stack criar. A versão de lançamento do Azure Stack mínima necessária para instalar esta versão do fornecedor de recursos MySQL está listada abaixo:

> |Versão mínima do Azure Stack|Versão do fornecedor de recursos de MySQL|
> |-----|-----|
> |Atualização de pilha 1808 (1.1808.0.97) do Azure|[1.1.30.0](https://aka.ms/azurestackmysqlrp11300)|
> |     |     |

> [!IMPORTANT]
> Aplicar a atualização com suporte do Azure Stack mínimo ao seu sistema integrado do Azure Stack ou implementar a mais recente do Azure Stack Development Kit (ASDK) antes de implementar a versão mais recente do fornecedor de recursos MySQL.

## <a name="new-features-and-fixes"></a>Novas funcionalidades e correções
Esta versão do fornecedor de recursos do MySQL do Azure Stack inclui as seguintes melhorias e correções:

- **Telemetria ativada para implementações de fornecedor de recursos do MySQL**. Coleção de telemetria tenha sido ativada para implementações de fornecedor de recursos do MySQL. Telemetria recolhida inclui a implementação de fornecedor de recursos, iniciar e parar vezes, sair do Estado, mensagens de saída e detalhes do erro (se aplicável).

- **Atualização de encriptação de TLS 1.2**. Ativado apenas 1.2 suporte TLS para a comunicação de fornecedor de recursos com componentes internos do Azure Stack. 

### <a name="fixes"></a>Correções

- **Fornecedor de recursos do MySQL do Azure Stack do PowerShell compatibilidade**. O fornecedor de recursos do MySQL foi atualizado para trabalho com o perfil de PowerShell híbrido do Azure Stack 2018-03-01- e para proporcionar compatibilidade com o AzureRM 1.3.0 e posterior.

- **Painel de palavra-passe de alteração de início de sessão de MySQL**. Foi corrigido um problema em que a palavra-passe não pode ser alterada no painel de palavra-passe de alteração. Notificações de alteração de links removidos da palavra-passe.

## <a name="known-issues"></a>Problemas conhecidos 

- **MySQL SKUs pode demorar até uma hora para ser visível no portal**. Pode demorar até uma hora para recentemente SKUs criados para ser visível para utilização durante a criação de novas bases de dados MySQL. 

    **Solução**: Nenhum.

- **Reutilizado inícios de sessão do MySQL**. Tentar criar um novo MySQL o início de sessão com o mesmo nome de utilizador como um início de sessão existente na mesma subscrição resultará em reutilizar o mesmo início de sessão e a palavra-passe existente. 

    **Solução**: Utilize nomes de utilizador diferente durante a criação de novos inícios de sessão na mesma subscrição ou criar inícios de sessão com o mesmo nome de utilizador em subscrições diferentes.

- **Requisito de suporte de TLS 1.2**. Se tentar implementar ou atualizar o fornecedor de recursos do MySQL a partir de um computador em que o TLS 1.2 não está ativada, a operação poderá falhar. Execute o seguinte comando do PowerShell no computador que está a ser utilizado para implementar ou atualizar o fornecedor de recursos para verificar que o TLS 1.2 é retornado como suportadas:

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  Se **Tls12** não é incluídos na saída do comando, TLS 1.2 não está ativada no computador.

    **Solução**: Execute o seguinte comando do PowerShell para ativar o TLS 1.2 e, em seguida, iniciar a implementação de fornecedor de recursos ou atualizar o script a partir da mesma sessão do PowerShell:

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```
 
### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Problemas conhecidos para os administradores de nuvem operacionais do Azure Stack
Consulte a documentação sobre o [notas de versão do Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Passos Seguintes
[Saiba mais sobre o fornecedor de recursos do MySQL](azure-stack-mysql-resource-provider.md).

[Preparar para implementar o fornecedor de recursos do MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Atualizar o fornecedor de recursos do MySQL a partir de uma versão anterior](azure-stack-mysql-resource-provider-update.md). 