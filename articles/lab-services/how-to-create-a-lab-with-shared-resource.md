---
title: Como criar um laboratório com um recurso partilhado Serviços de Laboratório Azure
description: Saiba como criar um laboratório que exija um recurso partilhado entre os alunos.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 9cb5698f95aa220208fb02a35a52ff5363a173ac
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85443371"
---
# <a name="how-to-create-a-lab-with-a-shared-resource-in-azure-lab-services"></a>Como criar um laboratório com um recurso partilhado nos Serviços de Laboratório Azure

Às vezes, ao criar um laboratório de sala de aula, pode haver alguns recursos que precisam de ser partilhados entre todos os alunos de um laboratório.  Por exemplo, tem um servidor de licenciamento ou um SQL Server para uma classe de base de dados.  Este artigo discutirá os passos para permitir o recurso partilhado para um laboratório.  Também falaremos sobre como limitar o acesso a esse recurso partilhado.

## <a name="architecture"></a>Arquitetura

Como mostrado no diagrama abaixo, teremos uma conta de laboratório com um laboratório.  A conta do laboratório terá as definições de observação de vnet para que a rede virtual para o laboratório esteja ligada à rede do recurso partilhado.  No diagrama abaixo existem duas redes virtuais com gamas IP não sobrepostas.  Estas gamas ip são apenas exemplos.  Note também que a rede virtual de recursos partilhados está na mesma subscrição que a conta de laboratório.

![Serviços de laboratório com arquitetura de recursos compartilhados](./media/how-to-create-a-lab-with-shared-resource/shared-resource-architecture.png)

## <a name="setup-shared-resource"></a>Recurso compartilhado de configuração

A rede virtual para o recurso partilhado deve ser criada antes da criação do laboratório.  Para obter mais informações sobre como criar uma rede virtual, consulte [criar uma rede virtual.](../virtual-network/quick-create-portal.md)  Planear gamas de rede virtuais para que não se sobreponham ao endereço IP das máquinas de laboratório é importante.  Para obter mais informações sobre o planeamento da sua rede, consulte o artigo de redes virtuais do [plano.](../virtual-network/virtual-network-vnet-plan-design-arm.md) No nosso exemplo, o recurso partilhado está numa rede virtual com o intervalo 10.2.0.0/16.  Se ainda não for feito, [crie uma sub-rede](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) para deter o recurso partilhado.  No exemplo, utilizamos a gama 10.2.0.0/24, mas a sua gama pode ser diferente dependendo das necessidades da sua rede.

O recurso partilhado pode ser um software em execução numa máquina virtual ou num serviço Azure fornecido. O recurso partilhado deve estar disponível através do endereço IP privado.  Ao disponibilizar o recurso partilhado apenas através de IP privado, limita o acesso a esse recurso partilhado.

O diagrama também mostra um grupo de segurança de rede (NSG) que pode ser usado para restringir o tráfego proveniente do VM do aluno.  Por exemplo, pode escrever uma regra de segurança que indique que o tráfego dos endereços IP do aluno VM só pode aceder a um recurso partilhado e nada mais.  Para obter mais informações sobre como definir regras de segurança, consulte [gerir o grupo de segurança da rede](../virtual-network/manage-network-security-group.md#work-with-security-rules). Se quiser restringir o acesso a um recurso partilhado a um laboratório específico, obtenha o endereço IP do laboratório a partir das [definições do laboratório a partir da conta do laboratório](manage-labs.md#view-labs-in-a-lab-account) e defina uma regra de entrada para permitir o acesso apenas a partir desse endereço IP.  Não se esqueça de permitir as portas 49152 a 65535 para esse endereço IP.  Opcionalmente, pode encontrar o endereço IP privado dos VM do aluno utilizando a [página de piscina de máquinas virtuais.](how-to-set-virtual-machine-passwords.md)

Se o seu recurso partilhado for uma máquina virtual Azure que executa o software necessário, poderá ter de modificar as regras de firewall padrão para a máquina virtual.

## <a name="lab-account"></a>Conta de Laboratório

Para utilizar um recurso partilhado, a conta de laboratório deve ser configurada para utilizar uma [rede virtual espreitada.](how-to-connect-peer-virtual-network.md)  Neste caso, vamos estar a espreitar para a rede virtual que detém o recurso partilhado.

>[!WARNING]
>O laboratório para a sua aula deve ser criado **depois** de a conta de laboratório ser espreitada para a rede virtual de recursos partilhados.  
Máquina de modelo

Uma vez que a sua conta de laboratório é espreitada para a rede virtual, a máquina de modelo deve agora ter acesso ao recurso partilhado.  Poderá ter de atualizar as regras de firewall, dependendo do recurso partilhado que está a ser acedido.
