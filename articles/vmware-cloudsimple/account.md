---
title: Gestão de conta - Portal Azure VMware Solutions (AVS)
description: Descreve como gerir contas no portal Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1fd7cb1c55fb664828448cef0b67ea9b16323bdf
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025372"
---
# <a name="manage-accounts-on-the-azure-vmware-solutions-avs-portal"></a>Gerir contas no portal Azure VMware Solutions (AVS)

Quando cria o seu serviço AVS, cria uma conta no AVS. A conta está associada à sua subscrição Azure onde o serviço está localizado. Todos os utilizadores com funções de proprietário e colaborador na subscrição têm acesso ao portal AVS. O ID de subscrição Azure e o ID do inquilino associados ao serviço AVS encontram-se na página Contas.

Para gerir contas no portal AVS, [aceda ao portal](access-cloudsimple-portal.md) e selecione **Conta** no menu lateral.

Selecione **Resumo** para visualizar informações sobre a configuração AVS da sua empresa. A capacidade atual da configuração da sua nuvem é mostrada, incluindo o número de Nuvens Privadas AVS, armazenamento total, configuração de cluster vSphere, número de nós e número de núcleos de computação. Um link está incluído para comprar nós adicionais se a configuração atual não atender a todas as suas necessidades.

## <a name="email-alerts"></a>Alertas de e-mail

Pode adicionar endereços de e-mail de quaisquer pessoas que queira notificar sobre alterações na configuração da Nuvem Privada AVS.

1. Na área de alertas de **e-mail adicional,** clique **em Adicionar novo**.
2. Insira o endereço de e-mail.
3. Imprensa De regresso.  

Para remover uma entrada, clique em **X**.

## <a name="avs-operator-access"></a>Acesso ao operador AVS

A definição de acesso do operador permite que o AVS o ajude na resolução de problemas, permitindo que um engenheiro de suporte assine o seu portal AVS. A definição está ativada por defeito. Todas as ações realizadas pelo engenheiro de suporte quando iniciars sessão na sua conta de cliente são registadas e disponíveis para a sua revisão na página de **Auditoria** ** > Atividade.**

Clique no acesso do **operador AVS habilitado** a alternar para ligar ou desligar o acesso.
