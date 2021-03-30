---
title: Fundamentos da Arquitetura nos Serviços do Laboratório Azure | Microsoft Docs
description: Este artigo cobrirá os recursos fundamentais utilizados pelos Serviços de Laboratório e a arquitetura básica de um laboratório.
author: emaher
ms.topic: overview
ms.date: 09/16/2020
ms.author: enewman
ms.openlocfilehash: 71d59e8bcf7b4078255d6b119e9d62f366e46033
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96437325"
---
# <a name="architecture-fundamentals-in-azure-lab-services"></a>Fundamentos da Arquitetura nos Serviços do Laboratório Azure

A Azure Lab Services é uma solução SaaS (software como serviço), o que significa que os recursos necessários pelos Serviços de Laboratório são tratados por si. Este artigo cobrirá os recursos fundamentais utilizados pelos Serviços de Laboratório e a arquitetura básica de um laboratório.  

A Azure Lab Services fornece algumas áreas que lhe permitem utilizar os seus próprios recursos em conjunto com os Serviços de Laboratório.  Para obter mais informações sobre a utilização de VMs na sua própria rede, consulte como [espreitar uma rede virtual.](how-to-connect-peer-virtual-network.md)  Para reutilizar imagens de uma Galeria de Imagens Partilhadas, veja como [anexar uma Galeria de Imagens Partilhadas.](how-to-attach-detach-shared-image-gallery.md)

Abaixo está a arquitetura básica de um laboratório de sala de aula.  A conta do laboratório está hospedada na sua assinatura. Os VMs do aluno, juntamente com os recursos necessários para apoiar os VMs estão hospedados numa subscrição propriedade da Lab Services. Vamos falar sobre o que está nas assinaturas do Serviço de Laboratório mais detalhadamente.

![arquitetura básica laboratórios](./media/classroom-labs-fundamentals/labservices-basic-architecture.png)

## <a name="hosted-resources"></a>Recursos Hospedados

Os recursos necessários para gerir um laboratório de sala de aula estão alojados numa das subscrições do Azure geridas pela Microsoft.  Os recursos incluem uma máquina virtual modelo para o instrutor, máquina virtual para cada aluno, e itens relacionados com a rede, tais como um equilibrador de carga, rede virtual e grupo de segurança de rede.  Estas assinaturas são monitorizadas para atividades suspeitas.  É importante notar que esta monitorização é feita externamente às máquinas virtuais através da extensão VM ou da monitorização dos padrões de rede.  Se o [desligamento da desconexão](how-to-enable-shutdown-disconnect.md) estiver ativado, ativa-se uma extensão de diagnóstico na máquina virtual. A extensão permite que os Serviços de Laboratório sejam informados do evento de desconexão do protocolo remoto de secretária (RDP).

## <a name="virtual-network"></a>Rede Virtual

Cada laboratório é isolado pela sua própria rede virtual.  Se o laboratório tem uma [rede virtual espreitada,](how-to-connect-peer-virtual-network.md)então cada laboratório é isolado pela sua própria sub-rede.  Os alunos ligam-se à sua máquina virtual através de um equilibrador de carga.  Nenhuma máquina virtual estudantil tem um endereço IP público; eles têm apenas um endereço IP privado.  A cadeia de ligação para o aluno será o endereço IP público do equilibrador de carga e uma porta aleatória entre 49152 e 65535.  As regras de entrada no equilibrador de carga encaminham a ligação, dependendo do sistema operativo, para a porta 22 (SSH) ou para a porta 3389 (PDR) da máquina virtual adequada. Um NSG impede o tráfego exterior em quaisquer outros portos.

## <a name="access-control-to-the-virtual-machines"></a>Controlo de acesso às máquinas virtuais

Os Serviços de Laboratório tratam da capacidade do aluno de realizar ações como iniciar e parar nas suas máquinas virtuais.  Também controla o acesso às informações de ligação VM.

Os Serviços de Laboratório também tratam do registo dos alunos ao serviço. Existem atualmente duas configurações de acesso diferentes: restritas e não restriadas. Para mais informações, consulte o artigo [de gestão de utilizadores](how-to-configure-student-usage.md#send-invitations-to-users) do laboratório. Acesso restrito significa que os Serviços de Laboratório verificam que os estudantes são adicionados como utilizador antes de permitir o acesso. Não retritos significa que qualquer utilizador pode registar-se desde que tenha o link de registo e haja capacidade no laboratório. Não retriced pode ser útil para eventos hackathon.

Os VMs dos estudantes que estão hospedados no laboratório da sala de aula têm um nome de utilizador e senha definidos pelo criador do laboratório.  Em alternativa, o criador do laboratório pode permitir que os estudantes registados escolham a sua própria senha no primeiro sº de sôr.000.  

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as funcionalidades disponíveis nos Serviços de Laboratório, consulte [os conceitos de Serviços Azure Lab](classroom-labs-concepts.md) e [a visão geral dos Serviços Azure Lab](classroom-labs-overview.md).
