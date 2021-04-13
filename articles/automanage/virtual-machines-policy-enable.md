---
title: Permitir a auto-produção de máquinas virtuais através da Política Azure
description: Saiba como permitir a auto-mutilação do Azure para VMs através de uma Política Azure incorporada no portal Azure.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 8846efa3619cec383809cdbd6efe70e3622fa007
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365200"
---
# <a name="enable-automanage-for-virtual-machines-through-azure-policy"></a>Permitir a auto-produção de máquinas virtuais através da Política Azure

Se quiser ativar a auto-produção de muitos VMs, pode fazê-lo utilizando uma [Política Azure](..\governance\azure-management.md)incorporada. Este artigo irá acompanhá-lo através da procura da política certa e como atribuí-la de forma a permitir a Automanage no portal Azure.


## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de começar.

> [!NOTE]
> As contas de teste gratuitas não têm acesso às máquinas virtuais utilizadas neste tutorial. Por favor, atualize para uma subscrição Pay-As-You-Go.

> [!IMPORTANT]
> É necessária a seguinte permissão Azure RBAC para ativar a autoadministração: Função **de proprietário** ou **colaborador,** juntamente com as funções **de Administrador de Acesso ao Utilizador.**

## <a name="direct-link-to-policy"></a>Ligação direta à Política
A definição de política de auto-produção pode ser encontrada no portal Azure com o nome de [máquinas virtuais Configure a bordo da Azure Automanage](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F270610db-8c04-438a-a739-e8e6745b22d3). Se clicar neste link, salte diretamente para o passo 8 no [Localizar e atribua a política abaixo.](#locate-and-assign-the-policy)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).


## <a name="locate-and-assign-the-policy"></a>Localizar e atribuir a apólice

1. Navegar para a **Política** no portal Azure
1. Vá ao painel **definições**
1. Clique nas **categorias** dropdown para ver as opções disponíveis
1. Selecione a opção **De Automanage - Azure máquina virtual de boas práticas**
1. Agora a lista irá atualizar para mostrar uma política incorporada com um nome que começa com *Enable Automanage...*
1. Clique no *Enable Automanage - Azure virtual machine best practices* built-in policy name
1. Depois de clicar na política, agora pode ver o **separador Definição**

    > [!NOTE]
    > A definição de Política Azure é usada para definir parâmetros de auto-manização como o perfil de configuração ou a conta. Também define filtros que asseguram que a política se aplica apenas aos VM corretos.

1. Clique no botão **Atribuir** para criar uma Atribuição
1. No **separador Básicos,** preencha **o Âmbito** definindo o Grupo *de Assinatura* e *Recursos*

    > [!NOTE]
    > O Âmbito permite definir a que VMs esta política se aplica. Pode definir a aplicação ao nível de subscrição ou ao nível do grupo de recursos. Se definir um grupo de recursos, todos os VMs que estão atualmente nesse grupo de recursos ou quaisquer VMs futuros que lhe adicionamos terão auto-gestão automaticamente ativada.

1. Clique no separador **Parâmetros** e desa estaba a **Conta de Auto-Produção** e o Perfil de **Configuração** pretendido
1. No **separador 'Rever +' criar,** rever as definições
1. Aplicar a Atribuição clicando em **Criar**
1. Ver as suas atribuições no **separador Atribuições** ao lado **da Definição**

> [!NOTE]
> Levará algum tempo para que essa política comece a produzir efeitos sobre os VMs atualmente no grupo de recursos ou na subscrição.


## <a name="next-steps"></a>Passos seguintes

Aprenda outra forma de ativar a auto-produção do Azure para máquinas virtuais através do portal Azure.

> [!div class="nextstepaction"]
> [Ativar a auto-produção de máquinas virtuais no portal Azure](quick-create-virtual-machines-portal.md)