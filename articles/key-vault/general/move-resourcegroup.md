---
title: Azure Key Vault movendo um cofre para um grupo de recursos diferente | Microsoft Docs
description: Orientação para mover um cofre chave para um grupo de recursos diferente.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: 14ecbcaa35153438601a3dabb70f5b38006ded1b
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749569"
---
# <a name="moving-an-azure-key-vault-across-resource-groups"></a>Mover um Azure Key Vault entre grupos de recursos

## <a name="overview"></a>Descrição Geral

Mover um cofre chave através de grupos de recursos é uma função de cofre de chaves suportada. Mover um cofre chave entre grupos de recursos não afetará a firewall do cofre ou as configurações da política de acesso. As aplicações ligadas e os principais de serviço devem continuar a funcionar como pretendido.

> [!IMPORTANT]
> **Os cofres-chave utilizados para encriptação do disco não podem ser movidos.**
> Se estiver a utilizar o cofre-chave com encriptação de disco para um VM, o cofre de teclas não pode ser movido para um grupo de recursos diferente ou uma subscrição enquanto a encriptação do disco estiver ativada. Tem de desativar a encriptação do disco antes de mover o cofre de chaves para um novo grupo de recursos ou subscrição. 

## <a name="design-considerations"></a>Considerações sobre Design

A sua organização pode ter implementado a Política Azure com aplicação ou exclusões ao nível do grupo de recursos. Pode haver um conjunto diferente de atribuições políticas no grupo de recursos onde o seu cofre-chave existe atualmente e o grupo de recursos onde está a mover o cofre principal. Um conflito nos requisitos políticos tem o potencial de quebrar as suas aplicações.

### <a name="example"></a>Exemplo

Tem uma aplicação ligada ao cofre-chave que cria certificados válidos por dois anos. O grupo de recursos onde está a tentar mover o cofre tem uma atribuição de política que bloqueia a criação de certificados válidos por mais de um ano. Depois de mover o seu cofre chave para o novo grupo de recursos, a operação para criar um certificado válido por dois anos será bloqueada por uma atribuição de política da Azure.

### <a name="solution"></a>Solução

Certifique-se de que vai à página da Política Azure no portal Azure e veja as atribuições políticas para o seu grupo de recursos atual, bem como o grupo de recursos para o qual se está a deslocar e certifique-se de que não existem desencontros.

## <a name="procedure"></a>Procedimento

1. Iniciar sessão no portal do Azure
2. Navegue até ao cofre da chave
3. Clique no separador "Visão Geral"
4. Selecione o botão "Mover"
5. Selecione "Mover-se para outro grupo de recursos" a partir das opções de dropdown
6. Selecione o grupo de recursos onde pretende mover o cofre de chaves
7. Reconheça o aviso sobre os recursos em movimento
8. Selecione "OK".

O Key Vault irá agora avaliar a validade do movimento de recursos e alertá-lo de quaisquer erros. Se não forem encontrados erros, o movimento de recursos será concluído. 
