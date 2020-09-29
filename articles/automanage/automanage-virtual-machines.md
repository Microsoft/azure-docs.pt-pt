---
title: Azure Automanage para máquinas virtuais
description: Saiba mais sobre a auto-produção da Azure para máquinas virtuais.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: 54a81ac36fb0b99f6931b372543dc3e4bca6a12c
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447914"
---
# <a name="azure-automanage-for-virtual-machines"></a>Azure Automanage para máquinas virtuais

Este artigo abrange informações sobre a Azure Automanage para máquinas virtuais, que têm os seguintes benefícios:

- Inteligentemente a bordo de máquinas virtuais para selecionar as melhores práticas Serviços Azure
- Configura automaticamente cada serviço por boas práticas do Azure
- Monitores para deriva e corrige para ele quando detetado
- Proporciona uma experiência simples (ponto, clique, definir, esquecer)


## <a name="overview"></a>Descrição geral

A Azure Automanage para máquinas virtuais é um serviço que elimina a necessidade de descobrir, saber a bordo e como configurar determinados serviços em Azure que beneficiariam a sua máquina virtual. Estes serviços ajudam a aumentar a fiabilidade, segurança e gestão de máquinas virtuais e são considerados serviços de boas práticas Azure, tais como [Azure Update Management](../automation/update-management/update-mgmt-overview.md) e [Azure Backup](../backup/backup-overview.md) - apenas para citar alguns.

Depois de embarcar as suas máquinas virtuais para a Azure Automanage, configura automaticamente cada serviço de boas práticas para as suas definições recomendadas. As melhores práticas são diferentes para cada um dos serviços. Um exemplo pode ser o Azure Backup, onde a melhor prática pode ser apoiar a máquina virtual uma vez por dia e ter um período de retenção de seis meses.

A Azure Automanage também monitoriza automaticamente a deriva e corrige-a quando detetada. O que isto significa é que se a sua máquina virtual estiver a bordo da Azure Automanage, não só a configuraremos de acordo com as melhores práticas do Azure, como monitorizaremos a sua máquina para garantir que continua a cumprir as melhores práticas em todo o seu ciclo de vida. Se a sua máquina virtual se desviar ou se desviar dessas práticas, corrigi-la-emos a sua máquina de volta para o estado desejado.

Por último, a experiência é incrivelmente simples.


## <a name="prerequisites"></a>Pré-requisitos

Existem vários pré-requisitos a considerar antes de tentar ativar a auto-produção do Azure nas suas máquinas virtuais.

- Apenas VMs do Servidor windows
- Os VMs devem estar a funcionar
- Os VMs devem estar numa região apoiada
- O utilizador deve ter permissões corretas
- Os VMs não devem ligar-se a um espaço de trabalho de análise de registo numa subscrição diferente
- Automanage não suporta subscrições sandbox neste momento

É necessário ter a função **Contribuinte** para permitir a auto-produção utilizando uma Conta de Automanagem existente. Se estiver a ativar a Automanage com uma nova Conta de Autoadministração, precisa das seguintes permissões: Função **de proprietário** ou **colaborador,** juntamente com as funções **de Administrador de Acesso ao Utilizador.**

É igualmente importante notar que a Automanage apenas suporta vMs Windows localizados nas seguintes regiões: Europa Ocidental, Leste dos EUA, Eua Ocidental 2, Canadá Central, Centro Ocidental DOS EUA.

## <a name="participating-services"></a>Serviços participantes

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services.png" alt-text="Inteligentemente a bordo de serviços.":::

Consulte [a Azure Automanage for Virtual Machines Best Practices](virtual-machines-best-practices.md) para a lista completa dos serviços Azure participantes, bem como os seus perfis de configuração suportados.

 Iremos automaticamente a bordo destes serviços participantes. São essenciais para as nossas melhores práticas, o Livro Branco, que pode encontrar no nosso [Quadro de Adoção](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/azure-server-management)em Nuvem.

Para todos estes serviços, iremos automaticamente embarcar, configurar automaticamente, monitorizar a deriva e mediar se for detetada deriva.


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Habilitação de Auto-produção de VMs no portal Azure

No portal Azure, pode ativar a Automanagem numa máquina virtual existente ou quando estiver a criar uma nova máquina virtual. Para obter passos concisos para este processo, consulte a [Automanage para máquinas virtuais de arranque rápido](quick-create-virtual-machines-portal.md).

Se é a primeira vez que permite a auto-produção para o seu VM, pode pesquisar no portal Azure para **automanage – Azure virtual machine as melhores práticas**. Clique **em Ativar em VM existente,** selecione os VMs que gostaria de embarcar, clique em **Select**, click **Enable**, e está feito.

A única altura em que poderá ter de interagir com este VM para gerir estes serviços é no caso de tentarmos remediar o seu VM, mas não o fizemos. Se remediarmos com sucesso o seu VM, vamos trazê-lo de volta ao cumprimento sem sequer alertá-lo.


## <a name="configuration-profiles"></a>Perfis de configuração

Quando está a ativar a auto-produção para a sua máquina virtual, é necessário um perfil de configuração. Os perfis de configuração são a base deste serviço. Eles definem exatamente quais os serviços a que estamos a bordo das suas máquinas e, em certa medida, qual seria a configuração desses serviços.

### <a name="default-configuration-profiles"></a>Perfis de configuração predefinidos

Existem dois perfis de configuração atualmente disponíveis.

- **As melhores práticas da máquina virtual Azure - O** perfil de configuração Dev/Test foi concebido para máquinas Dev/Test.
- **Azure máquina virtual boas práticas - Perfil de** configuração de produção é para produção.

A razão para este diferenciador é porque certos serviços são recomendados com base na carga de trabalho em execução. Por exemplo, numa máquina de Produção, embarcaremos automaticamente para o Azure Backup. No entanto, para uma máquina Dev/Test, um serviço de backup seria um custo desnecessário, uma vez que as máquinas Dev/Test são tipicamente menores no negócio.

### <a name="customizing-a-configuration-profile-using-preferences"></a>Personalizar um perfil de configuração usando preferências

Além dos serviços padrão a que estamos a bordo, permitimos-lhe configurar um certo subconjunto de preferências. Estas preferências são permitidas dentro de um leque de opções de configuração que não violam as nossas melhores práticas. Por exemplo, no caso do Azure Backup permitir-lhe-emos definir a frequência da cópia de segurança e em que dia da semana ocorre. No entanto, *não* permitiremos que desligue completamente o Azure Backup.

> [!NOTE]
> No perfil de configuração Dev/Test, não vamos fazer backup do VM.

Pode ajustar as definições de um perfil de configuração predefinido através de preferências. Saiba como criar uma preferência [aqui.](virtual-machines-custom-preferences.md)

> [!NOTE]
> Não é possível alterar o perfil de configuração do seu VM enquanto a automanagem estiver ativada. Terá de desativar a auto-mutilação para esse VM e, em seguida, voltar a ativar a Automanage com o perfil e preferências de configuração pretendidos.


## <a name="automanage-account"></a>Conta de Auto-Produção

A Conta De Auto-Produção é o contexto de segurança ou a identidade em que ocorrem as operações automatizadas. Normalmente, a opção Conta De Gestão Automática é desnecessária para você selecionar, mas se havia um cenário de delegação onde você queria dividir a gestão automatizada (talvez entre dois administradores de sistema), esta opção permite definir uma identidade Azure para cada um desses administradores.

Na experiência do portal Azure, quando está a permitir a auto-managem nos seus VMs, existe uma queda avançada na lâmina **de boas práticas enable Azure VM** que lhe permite atribuir ou criar manualmente a Conta de Auto-managem.

> [!NOTE]
> É necessário ter a função **Contribuinte** para permitir a auto-produção utilizando uma Conta de Automanagem existente. Se estiver a ativar a Automanage com uma nova Conta de Autoadministração, precisa das seguintes permissões: Função **de proprietário** ou **colaborador,** juntamente com as funções **de Administrador de Acesso ao Utilizador.**


## <a name="status-of-vms"></a>Estatuto dos VM

No portal Azure, vá à página **de boas práticas da máquina virtual Automanage – Azure** que lista todos os seus VMs geridos automaticamente. Aqui verá o estado geral de cada máquina virtual.

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="Inteligentemente a bordo de serviços.":::

Para cada VM listado, são apresentados os seguintes detalhes: Nome, perfil de configuração, preferência de configuração, Estado, Conta, Subscrição e Grupo de Recursos.

A coluna **Status** pode apresentar os seguintes estados:
- *Em curso* - o VM acabou de ser ativado e está a ser configurado
- *Configurado* - o VM está configurado e não é detetada nenhuma deriva
- *Falhado* - o VM tem derivado e nós não fomos capazes de remediar

Se vir o **Estado** como *Falhado,* pode resolver problemas de resolução através do Grupo de Recursos onde se encontra o seu VM. Vá aos **grupos de Recursos**, selecione o seu grupo de recursos, clique em **Implementações** e veja o estado *de Falha* lá juntamente com detalhes de erro.


## <a name="disabling-automanage-for-vms"></a>Automanagem incapacitante para VMs

Pode decidir um dia desativar a auto-mutilação em certos VMs. Por exemplo, a sua máquina está a executar uma carga de trabalho super sensível e segura e precisa de a bloquear ainda mais do que a Azure teria feito naturalmente, por isso precisa de configurar a máquina fora das melhores práticas do Azure.

Para isso no portal Azure, vá à página **de boas práticas da máquina virtual Automanage – Azure** que lista todos os seus VMs geridos automaticamente. Selecione a caixa de verificação ao lado da máquina virtual que pretende desativar a partir da gestão automática e, em seguida, clique no botão **de gestão automática desativada.**

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Inteligentemente a bordo de serviços.":::

Leia atentamente através das mensagens no pop-up resultante antes de concordar com o **Desactivamento**.

```
Disabling automanagement in a VM results in the following behavior:

1.    The configuration of the VM and the services it's onboarded to will not be changed
2.    Any changes incurred by those services will remain billable and will continue to be incurred
3.    Any Automanage behaviors will stop immediately
```

Em primeiro lugar, não vamos desabar a máquina virtual de nenhum dos serviços a que a embarcamos e configuramos. Assim, quaisquer encargos incorridos por esses serviços continuarão a ser faturadas. Terá de sair do quadro, se necessário. Qualquer comportamento de auto-condutor parará imediatamente. Por exemplo, deixaremos de monitorizar o VM para deriva.


## <a name="next-steps"></a>Próximos passos

Neste artigo, você descobriu que a autogestão para máquinas virtuais fornece um meio para o qual você pode eliminar a necessidade de você saber, a bordo, e configurar as melhores práticas serviços Azure. Além disso, se uma máquina que a bordo a bordo de Automanage for Virtual Machines deriva dos perfis de configuração configurados, nós automaticamente a traremos de volta ao cumprimento.

Tente ativar a auto-produção de máquinas virtuais no portal Azure.

> [!div class="nextstepaction"]
> [Ativar a auto-produção de máquinas virtuais no portal Azure](quick-create-virtual-machines-portal.md)
