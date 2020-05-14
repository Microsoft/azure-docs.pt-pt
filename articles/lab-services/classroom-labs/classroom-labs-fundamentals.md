---
title: Fundamentos da Arquitetura nos Serviços de Laboratório Azure [ Microsoft Docs
description: Este artigo abrangerá os recursos fundamentais utilizados pelos Serviços de Laboratório e a arquitetura básica de um laboratório.
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: overview
ms.date: 5/10/2020
ms.author: enewman
ms.openlocfilehash: fa980b038d6c3e68cb001652a4121fc41a06d425
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115099"
---
# <a name="architecture-fundamentals-in-azure-lab-services"></a>Fundamentos da Arquitetura nos Serviços de Laboratório Azure

O Azure Lab Services é uma solução SaaS (software como serviço), o que significa que os recursos necessários pelos Serviços lab são tratados por si. Este artigo abrangerá os recursos fundamentais utilizados pelos Serviços de Laboratório e a arquitetura básica de um laboratório.  

A Azure Lab Services fornece algumas áreas que lhe permitem utilizar os seus próprios recursos em conjunto com os Serviços de Laboratório.  Para obter mais informações sobre a utilização de VMs na sua própria rede, consulte como [utilizar uma rede virtual](how-to-connect-peer-virtual-network.md).  Para reutilizar imagens de uma Galeria de Imagem Partilhada, veja como [anexar uma Galeria](how-to-attach-detach-shared-image-gallery.md)de Imagem Partilhada.

Abaixo está a arquitetura básica de um laboratório de sala de aula.  A conta do laboratório está alojada na sua assinatura. Os VMs estudantis, juntamente com os recursos necessários para apoiar os VMs estão hospedados numa subscrição propriedade da Lab Services. Vamos falar mais detalhadamente sobre o que está nas assinaturas do Serviço lab.

![Laboratórios de sala de aula arquitetura básica](../media/classroom-labs-fundamentals/labservices-basic-architecture.png)

## <a name="hosted-resources"></a>Recursos Hospedados

Os recursos necessários para executar um laboratório de sala de aula estão hospedados numa das subscrições azure geridas pela Microsoft.  Os recursos incluem uma máquina virtual modelo para o instrutor, máquina virtual para cada aluno, e itens relacionados com a rede, tais como um equilibrador de carga, rede virtual e grupo de segurança de rede.  Estas assinaturas são monitorizadas para atividades suspeitas.  É importante notar que esta monitorização é feita externamente às máquinas virtuais através da monitorização da extensão VM ou do padrão de rede.  Se o [desligamento estiver](how-to-enable-shutdown-disconnect.md) ativado, ativa-se uma extensão de diagnóstico na máquina virtual. A extensão permite que os Serviços lab sejam informados do evento de desconexão da sessão de desconexão do protocolo de ambiente de trabalho remoto (RDP).

## <a name="virtual-network"></a>Rede Virtual

Cada laboratório é isolado pela sua própria rede virtual.  Se o laboratório tem uma [rede virtual esparsa,](how-to-connect-peer-virtual-network.md)então cada laboratório é isolado pela sua própria sub-rede.  Os alunos ligam-se à sua máquina virtual através de um equilibrador de carga.  Nenhuma máquina virtual estudantil tem um endereço IP público; só têm um endereço ip privado.  A cadeia de ligação para o aluno será o endereço IP público do equilibrador de carga e uma porta aleatória entre 49152 e 65535.  As regras de entrada no equilibrante de carga encaminharam a ligação, dependendo do sistema operativo, para a porta 22 (SSH) ou a porta 3389 (RDP) da máquina virtual adequada. Um NSG impede o tráfego externo em quaisquer outros portos.

## <a name="access-control-to-the-virtual-machines"></a>Controlo de acesso às máquinas virtuais

A Lab Services lida com a capacidade do aluno de realizar ações como iniciar e parar nas suas máquinas virtuais.  Também controla o acesso às suas informações de ligação VM.

Os Serviços de Laboratório também tratam do registo de alunos ao serviço. Existem atualmente duas configurações de acesso diferentes: restritas e sem restrições. Para mais informações, consulte o artigo dos utilizadores do laboratório de [gestão.](how-to-configure-student-usage.md#send-invitations-to-users) Acesso restrito significa que os Serviços lab verificam que os alunos são adicionados como utilizadores antes de permitir o acesso. Significa que qualquer utilizador pode registar-se desde que tenha a ligação de registo e exista capacidade no laboratório. Sem restrições pode ser útil para eventos de hackathon.

Os VMs estudantis que estão hospedados no laboratório de sala de aula têm um nome de utilizador e senha definido pelo criador do laboratório.  Alternadamente, o criador do laboratório pode permitir que os estudantes registados escolham a sua própria senha no primeiro início de inscrição.  

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre as funcionalidades disponíveis nos Serviços de Laboratório, consulte os conceitos de [Serviços de Laboratório Azure](classroom-labs-concepts.md) e a visão geral dos Serviços de [Laboratório Azure.](classroom-labs-overview.md)
