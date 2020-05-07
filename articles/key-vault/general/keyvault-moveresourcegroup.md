---
title: Cofre chave Azure movendo um cofre para um grupo de recursos diferentes Microsoft Docs
description: Orientação sobre mover um cofre chave para um grupo de recursos diferente.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another resource group.
ms.openlocfilehash: bbc27af9eb448911093473d6ab20fde8004c7b88
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783724"
---
# <a name="moving-an-azure-key-vault-across-resource-groups"></a>Mover um Cofre chave Azure através de grupos de recursos

## <a name="overview"></a>Descrição geral

Mover um cofre chave através de grupos de recursos é uma característica de cofre suportada. Mover um cofre chave entre grupos de recursos não afetará a firewall do cofre chave ou as configurações da política de acesso. As aplicações e os diretores de serviço sinuosos devem continuar a funcionar como pretendido.

## <a name="design-considerations"></a>Considerações sobre Design

A sua organização pode ter implementado a Política Azure com aplicação ou exclusões a nível do grupo de recursos. Pode haver um conjunto diferente de missões políticas no grupo de recursos onde o seu cofre chave existe atualmente e o grupo de recursos onde você está movendo o seu cofre chave. Um conflito nos requisitos políticos tem o potencial de quebrar as suas aplicações.

### <a name="example"></a>Exemplo

Tem uma aplicação ligada ao cofre chave que cria certificados válidos por dois anos. O grupo de recursos onde está a tentar mover o seu cofre chave tem uma atribuição de política que bloqueia a criação de certificados válidos por mais de um ano. Depois de mover o seu cofre chave para o novo grupo de recursos, a operação para criar um certificado válido por dois anos será bloqueada por uma atribuição de política azure.

### <a name="solution"></a>Solução

Certifique-se de que vai à página da Política Azure no portal Azure e analise as atribuições políticas para o seu grupo de recursos atuais, bem como o grupo de recursos para o qual está a mover-se e certifique-se de que não há desfasamentos.

## <a name="procedure"></a>Procedimento

1. Iniciar sessão no portal do Azure
2. Navegue para o seu cofre chave
3. Clique no separador "Visão Geral"
4. Selecione o botão "Mover"
5. Selecione "Move to another resource group" a partir das opções de dropdown
6. Selecione o grupo de recursos onde pretende mover o seu cofre chave
7. Reconheça o aviso sobre os recursos móveis
8. Selecione "OK"

O Cofre Chave irá agora avaliar a validade do movimento de recursos e alertá-lo de quaisquer erros. Se não forem detetados erros, o movimento de recursos será concluído. 
