---
title: Gerenciamento de conta-solução do Azure VMware por portal do CloudSimple
description: Descreve como gerenciar contas na solução VMware do Azure pelo portal do CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 00348d36c38fdbfdf69a2e4e80a4c720268b40c8
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69564189"
---
# <a name="manage-accounts-on-the-azure-vmware-solution-by-cloudsimple-portal"></a>Gerenciar contas na solução VMware do Azure pelo portal do CloudSimple

Quando você cria o serviço CloudSimple, ele cria uma conta no CloudSimple. A conta está associada à sua assinatura do Azure onde o serviço está localizado. Todos os usuários com funções de proprietário e colaborador na assinatura têm acesso ao portal do CloudSimple. A ID de assinatura do Azure e a ID de locatário associada ao serviço CloudSimple são encontradas na página contas.

Para gerenciar contas no portal do CloudSimple, [acesse o portal](access-cloudsimple-portal.md) e selecione **conta** no menu lateral.

Selecione **Resumo** para exibir informações sobre a configuração de CloudSimple da sua empresa. A capacidade atual de sua configuração de nuvem é mostrada, incluindo o número de nuvens privadas, o armazenamento total, a configuração de cluster vSphere, o número de nós e o número de núcleos de computação. Um link é incluído para comprar nós adicionais se a configuração atual não atender a todas as suas necessidades.

## <a name="email-alerts"></a>Alertas de email

Você pode adicionar endereços de email de qualquer pessoa que gostaria de notificar sobre as alterações na configuração da nuvem privada.

1. Na área **alertas de email adicionais** , clique em **Adicionar novo**.
2. Insira o endereço de email.
3. Pressione retornar.  

Para remover uma entrada, clique em **X**.

## <a name="cloudsimple-operator-access"></a>Acesso do operador CloudSimple

A configuração de acesso do operador permite que o CloudSimple o ajude a solucionar problemas permitindo que um engenheiro de suporte entre no seu portal do CloudSimple.  A configuração é habilitada por padrão. Todas as ações executadas pelo engenheiro de suporte quando conectado à sua conta de cliente são registradas e disponíveis para sua análise na página**auditoria** de **atividade** > .

Clique na opção **acesso do operador CloudSimple habilitado** para ativar ou desativar o acesso.
