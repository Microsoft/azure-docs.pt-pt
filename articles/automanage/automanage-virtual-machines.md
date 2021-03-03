---
title: Azure Automanage para máquinas virtuais
description: Saiba mais sobre a auto-produção da Azure para máquinas virtuais.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: 1d3b2174df5dd83852ce120ec6693ae187a3e795
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101643535"
---
# <a name="azure-automanage-for-virtual-machines"></a>Azure Automanage para máquinas virtuais

Este artigo abrange informações sobre a Azure Automanage para máquinas virtuais, que têm os seguintes benefícios:

- Inteligentemente a bordo de máquinas virtuais para selecionar as melhores práticas Serviços Azure
- Configura automaticamente cada serviço por boas práticas do Azure
- Monitores para deriva e corrige para ele quando detetado
- Proporciona uma experiência simples (ponto, clique, definir, esquecer)


## <a name="overview"></a>Descrição Geral

A Azure Automanage para máquinas virtuais é um serviço que elimina a necessidade de descobrir, saber a bordo e como configurar determinados serviços em Azure que beneficiariam a sua máquina virtual. Estes serviços são considerados como serviços de boas práticas da Azure, e ajudam a aumentar a fiabilidade, segurança e gestão de máquinas virtuais. Os serviços de exemplo incluem [Azure Update Management](../automation/update-management/overview.md) e [Azure Backup](../backup/backup-overview.md).

Depois de embarcar as suas máquinas virtuais para a Azure Automanage, cada serviço de boas práticas está configurado para as suas definições recomendadas. As melhores práticas são diferentes para cada um dos serviços. Um exemplo pode ser o Azure Backup, onde a melhor prática pode ser apoiar a máquina virtual uma vez por dia e ter um período de retenção de seis meses.

A Azure Automanage também monitoriza automaticamente a deriva e corrige-a quando detetada. O que isto significa é que se a sua máquina virtual estiver a bordo da Azure Automanage, não só a configuraremos de acordo com as melhores práticas do Azure, como monitorizaremos a sua máquina para garantir que continua a cumprir as melhores práticas em todo o seu ciclo de vida. Se a sua máquina virtual se desviar ou se desviar dessas práticas (por exemplo, se um serviço estiver desincêndido), corrigi-la-emos a sua máquina de volta para o estado pretendido.

## <a name="prerequisites"></a>Pré-requisitos

Existem vários pré-requisitos a considerar antes de tentar ativar a auto-produção do Azure nas suas máquinas virtuais.

- Versões suportadas [do Windows Server](automanage-windows-server.md#supported-windows-server-versions) e [distros Linux](automanage-linux.md#supported-linux-distributions-and-versions)
- Os VM devem estar numa região apoiada (ver abaixo)
- O utilizador deve ter permissões corretas (ver abaixo)
- Automanage não suporta subscrições sandbox neste momento

### <a name="supported-regions"></a>Regiões suportadas
A automanagem suporta apenas VMs localizados nas seguintes regiões:
* Europa Ocidental
* Europa do Norte
* E.U.A. Central
* E.U.A. Leste
* E.U.A. Leste 2
* E.U.A. Oeste
* E.U.A. Oeste 2
* Canadá Central
* E.U.A. Centro-Oeste
* E.U.A. Centro-Sul
* Leste do Japão
* Sul do Reino Unido
* AU Leste
* AU Sudeste

### <a name="required-rbac-permissions"></a>Permissões necessárias do RBAC
A sua conta requer funções RBAC ligeiramente diferentes, dependendo se está a permitir a Automanage com uma nova conta Automanage.

Se estiver a habilitar a Automanage com uma nova conta Automanage:
* **Papel do proprietário** na(s) subscrição(s) contendo os seus VMs, _**ou**_
* **Funções de** Administrador de Contribuinte e Acesso ao **Utilizador** nas subscrições que contêm os seus VMs

Se estiver a habilitar a Automanage com uma conta automanage existente:
* **Papel do contribuinte** no grupo de recursos que contém os seus VMs

> [!NOTE]
> Se pretender utilizar a Automanage num VM que esteja ligado a um espaço de trabalho numa subscrição diferente, deve ter as permissões acima descritas em cada subscrição.

## <a name="participating-services"></a>Serviços participantes

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services.png" alt-text="Inteligentemente a bordo de serviços.":::

Para a lista completa dos serviços Azure participantes, bem como o seu ambiente apoiado, consulte o seguinte:
- [Automanagem para Linux](automanage-linux.md)
- [Automanagem para servidor do Windows](automanage-windows-server.md)

 Iremos automaticamente a bordo destes serviços participantes. São essenciais para as nossas melhores práticas, o Livro Branco, que pode encontrar no nosso [Quadro de Adoção](/azure/cloud-adoption-framework/manage/azure-server-management)em Nuvem.

Para todos estes serviços, iremos automaticamente embarcar, configurar automaticamente, monitorizar a deriva e mediar se for detetada deriva.


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Habilitação de Auto-produção de VMs no portal Azure

No portal Azure, pode ativar a Automanagem numa máquina virtual existente ou quando estiver a criar uma nova máquina virtual. Para obter passos concisos para este processo, consulte a [Automanage para máquinas virtuais de arranque rápido](quick-create-virtual-machines-portal.md).

Se é a primeira vez que permite a auto-produção para o seu VM, pode pesquisar no portal Azure para **automanage – Azure virtual machine as melhores práticas**. Clique **em Ativar em VM existente,** selecione os VMs que gostaria de embarcar, clique em **Select**, click **Enable**, e está feito.

A única altura em que poderá ter de interagir com este VM para gerir estes serviços é no caso de tentarmos remediar o seu VM, mas não o fizemos. Se remediarmos com sucesso o seu VM, vamos trazê-lo de volta ao cumprimento sem sequer alertá-lo. Para mais detalhes, consulte [o Estado dos VMs.](#status-of-vms)


## <a name="environment-configuration"></a>Configuração do ambiente

Quando está a ativar a auto-produção para a sua máquina virtual, é necessário um ambiente. Os ambientes são a base deste serviço. Eles definem quais os serviços a que a bordo das suas máquinas e, em certa medida, qual seria a configuração desses serviços.

### <a name="default-environments"></a>Ambientes predefinidos

Existem atualmente dois ambientes disponíveis.

- O ambiente **Dev/Test** foi concebido para máquinas Dev/Test.
- O ambiente **de produção** é para produção.

A razão para este diferenciador é porque certos serviços são recomendados com base na carga de trabalho em execução. Por exemplo, numa máquina de Produção, embarcaremos automaticamente para o Azure Backup. No entanto, para uma máquina Dev/Test, um serviço de backup seria um custo desnecessário, uma vez que as máquinas Dev/Test são tipicamente menores no negócio.

### <a name="customizing-an-environment-using-preferences"></a>Personalizar um ambiente usando preferências

Além dos serviços padrão a que estamos a bordo, permitimos-lhe configurar um certo subconjunto de preferências. Estas preferências são permitidas dentro de um leque de opções de configuração. Por exemplo, no caso do Azure Backup permitir-lhe-emos definir a frequência da cópia de segurança e em que dia da semana ocorre.

> [!NOTE]
> No ambiente Dev/Test, não vamos apoiar o VM.

Pode ajustar as definições de um ambiente predefinido através de preferências. Saiba como criar uma preferência [aqui.](virtual-machines-custom-preferences.md)

> [!NOTE]
> Não é possível alterar a configuração de enivonrment no seu VM enquanto a automanagem estiver ativada. Terá de desativar a auto-mutilação para esse VM e, em seguida, reativar a auto-produção com o ambiente e preferências pretendidos.

Para obter a lista completa dos serviços Azure participantes e se apoiarem preferências, consulte aqui:
- [Automanagem para Linux](automanage-windows-server.md)
- [Automanagem para servidor do Windows](automanage-windows-server.md)


## <a name="automanage-account"></a>Conta de Auto-Produção

A Conta De Auto-Produção é o contexto de segurança ou a identidade em que ocorrem as operações automatizadas. Normalmente, a opção Conta De Gestão Automática é desnecessária para você selecionar, mas se havia um cenário de delegação onde você queria dividir a gestão automatizada dos seus recursos (talvez entre dois administradores de sistema), esta opção permite definir uma identidade Azure para cada um desses administradores.

Na experiência do portal Azure, quando está a permitir a auto-managem nos seus VMs, existe uma queda avançada na lâmina **de boas práticas enable Azure VM** que lhe permite atribuir ou criar manualmente a Conta de Auto-managem.

A Conta de Gestão Automática receberá **as** funções de Contribuinte e **Dedução de Política de Recursos** para a(s) subscrição(s) que contém a(s) máquina(s) que você está a bordo para a Automanage. Pode utilizar a mesma Conta de Auto-gestão em máquinas através de várias subscrições, o que dará permissãos de **Contribuinte** de Conta de Auto-Gestão e **Contribuidor de Política de Recursos** em todas as subscrições.

Se o seu VM estiver ligado a um espaço de trabalho log Analytics noutra subscrição, a Conta De Gestão Automática também receberá **contribuidor** como **colaborador de política de recursos** nessa outra subscrição.

Se estiver a habilitar a autoadministração com uma nova Conta de Autoadministração, necessita das seguintes permissões na sua subscrição: Função **de proprietário** ou **colaborador,** juntamente com as funções **de Administrador de Acesso ao Utilizador.**

Se estiver a ativar a Automanage com uma Conta de Auto-gestão existente, tem de ter a função **de Contribuinte** no grupo de recursos que contém os seus VMs.

> [!NOTE]
> Quando desativar as melhores práticas de auto-managem, as permissões da Conta De Auto-Manage em quaisquer subscrições associadas permanecerão. Remova manualmente as permissões indo para a página IAM da subscrição ou eliminando a Conta Desmanvação Automática. A Conta de Gestão Automática não pode ser eliminada se ainda estiver a gerir máquinas.


## <a name="status-of-vms"></a>Estatuto dos VM

No portal Azure, vá à página **de boas práticas da máquina virtual Automanage – Azure** que lista todos os seus VMs geridos automaticamente. Aqui verá o estado geral de cada máquina virtual.

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="Lista de máquinas virtuais configuradas.":::

Para cada VM listado, são apresentados os seguintes detalhes: Nome, Ambiente, Preferência de configuração, Estado, Sistema Operativo, Conta, Subscrição e Grupo de Recursos.

A coluna **Status** pode apresentar os seguintes estados:
- *Em curso* - o VM acabou de ser ativado e está a ser configurado
- *Configurado* - o VM está configurado e não é detetada nenhuma deriva
- *Falhado* - o VM tem derivado e nós não fomos capazes de remediar
- *Pendente* - o VM não está atualmente em execução, e a Automanage tentará embarcar ou remediar o VM quando for o próximo a funcionar

Se vir o **Estado** como *Falhado,* pode resolver problemas de resolução através do Grupo de Recursos onde se encontra o seu VM. Vá aos **grupos de Recursos**, selecione o seu grupo de recursos, clique em **Implementações** e veja o estado *de Falha* lá juntamente com detalhes de erro.


## <a name="disabling-automanage-for-vms"></a>Automanagem incapacitante para VMs

Pode decidir um dia desativar a auto-mutilação em certos VMs. Por exemplo, a sua máquina está a executar uma carga de trabalho super sensível e segura e precisa de a bloquear ainda mais do que a Azure teria feito naturalmente, por isso precisa de configurar a máquina fora das melhores práticas do Azure.

Para isso no portal Azure, vá à página **de boas práticas da máquina virtual Automanage – Azure** que lista todos os seus VMs geridos automaticamente. Selecione a caixa de verificação ao lado da máquina virtual que pretende desativar a partir da gestão automática e, em seguida, clique no botão **de gestão automática desativada.**

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Desativar a auto-produção numa máquina virtual.":::

Leia atentamente através das mensagens no pop-up resultante antes de concordar com o **Desactivamento**.

> [!NOTE]
> A desativação da auto-gestão num VM resulta no seguinte comportamento:
>
> - A configuração do VM e os serviços a bordo não mudam.
> - Quaisquer encargos incorridos por esses serviços continuam a ser faturadas e continuam a ser incorridos.
> - Qualquer comportamento de auto-condutor para imediatamente.


Em primeiro lugar, não vamos desabar a máquina virtual de nenhum dos serviços a que a embarcamos e configuramos. Assim, quaisquer encargos incorridos por esses serviços continuarão a ser faturadas. Terá de sair do quadro, se necessário. Qualquer comportamento de auto-condutor parará imediatamente. Por exemplo, deixaremos de monitorizar o VM para deriva.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você descobriu que a autogestão para máquinas virtuais fornece um meio para o qual você pode eliminar a necessidade de você saber, a bordo, e configurar as melhores práticas serviços Azure. Além disso, se uma máquina que a bordo a bordo deu à Automanage para máquinas virtuais derivar da configuração do ambiente, nós automaticamente a traremos de volta ao cumprimento.

Tente ativar a auto-produção de máquinas virtuais no portal Azure.

> [!div class="nextstepaction"]
> [Ativar a auto-produção de máquinas virtuais no portal Azure](quick-create-virtual-machines-portal.md)