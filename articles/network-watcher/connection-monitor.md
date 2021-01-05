---
title: Tutorial - Monitorizar a comunicação da rede utilizando o portal Azure
description: Neste tutorial, aprenda a monitorizar a comunicação de rede entre duas máquinas virtuais com a capacidade do monitor de ligação do Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to monitor communication between a VM and another VM. If the communication fails, I need to know why, so that I can resolve the problem.
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.custom: mvc
ms.openlocfilehash: 24b1549b2e460bc0e72fb76f5437b15838604949
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97896367"
---
# <a name="tutorial-monitor-network-communication-between-two-virtual-machines-using-the-azure-portal"></a>Tutorial: Monitorizar a comunicação de rede entre duas máquinas virtuais com o portal do Azure

> [!NOTE]
> Esta capa tutorial Connection Monitor (clássico). Experimente o novo e melhorado [Monitor de Conexão](connection-monitor-overview.md) para experimentar uma monitorização melhorada da conectividade

A comunicação com êxito entre uma máquina virtual (VM) e um ponto final, como outra VM, pode ser fundamental para a sua organização. Por vezes, são introduzidas alterações de configuração que podem interromper a comunicação. Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Criar duas VMs
> * Monitorizar a comunicação entre as VMs com a capacidade de monitor de ligação do Observador de Rede
> * Gerar alertas em métricas de Monitor de Ligação
> * Diagnosticar um problema de comunicação entre duas VMs e aprender a resolvê-lo

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.



## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-vms"></a>Criar VMs

Crie duas VMs.

### <a name="create-the-first-vm"></a>Criar a primeira VM

1. Selecione **+ Criar um recurso**, disponível no canto superior esquerdo do portal do Azure.
2. Selecione **Computação** e, em seguida, selecione um sistema operativo. Neste tutorial, é utilizado o **Windows Server 2016 Datacenter**.
3. Introduza ou selecione as seguintes informações, aceite as predefinições para as definições restantes e, em seguida, selecione **OK**:

    |Definição|Valor|
    |---|---|
    |Nome|myVm1|
    |Nome de utilizador| Introduza um nome de utilizador à sua escolha.|
    |Palavra-passe| Introduza uma palavra-passe à sua escolha. A palavra-passe deve ter pelo menos 12 caracteres de comprimento e satisfazer os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Subscrição| Selecione a sua subscrição.|
    |Grupo de recursos| Selecione **Criar novo** e introduza **myResourceGroup**.|
    |Localização| Selecione **E.U.A. Leste**.|

4. Escolha um tamanho para a VM e selecione **Selecionar**.
5. Em **Definições**, selecione **Extensões**. Selecione **Adicionar extensão** e selecione **Agente do Observador de Rede para Windows**, conforme mostra a imagem seguinte:

    ![Extensão do agente do Observador de Rede](./media/connection-monitor/nw-agent-extension.png)

6. Em **Agente do Observador de Rede para Windows**, selecione **Criar**, em **Instalar extensão** selecione **OK** e, em seguida, em **Extensões**, selecione **OK**.
7. Aceite as predefinições nas restantes **Definições** e selecione **OK**.
8. Em **Criar** no **Resumo**, selecione **Criar** para iniciar a implementação da VM.

### <a name="create-the-second-vm"></a>Criar a segunda VM

Execute novamente os passos em [Criar a primeira VM](#create-the-first-vm), com as seguintes alterações:

|Passo|Definição|Valor|
|---|---|---|
| 1 | Selecione uma versão do **Ubuntu Server** |                                                                         |
| 3 | Nome                                  | myVm2                                                                   |
| 3 | Tipo de autenticação                   | Cole a chave pública SSH ou selecione **Palavra-passe** e introduza uma palavra-passe. |
| 3 | Grupo de recursos                        | Selecione **Utilizar existente** e selecione **myResourceGroup**.                 |
| 6 | Extensões                            | **Agente observador de rede para Linux**                                             |

A implementação da VM demora alguns minutos. Aguarde que a VM conclua a implementação antes de continuar com os restantes passos.

## <a name="create-a-connection-monitor"></a>Criar um monitor de ligação

Crie um monitor de ligação para monitorizar a comunicação através da porta TCP 22 de *myVm1* para *myVm2*.

1. No lado esquerdo do portal, selecione **Todos os serviços**.
2. Comece a escrever *observador de rede* na caixa **Filtro**. Quando o **Observador de Rede** aparecer nos resultados de pesquisa, selecione-o.
3. Em **MONITORIZAÇÃO**, selecione **Monitor de ligação**.
4. Selecione **+ Adicionar**.
5. Introduza ou selecione as informações da ligação que pretende monitorizar e, em seguida, selecione **Adicionar**. No exemplo apresentado na imagem seguinte, a ligação monitorizada é da VM *myVm1* para a VM *myVm2* através da porta 22:

    | Definição                  | Valor               |
    | ---------                | ---------           |
    | Nome                     | myVm1-myVm2(22)     |
    | Origem                   |                     |
    | Máquina virtual          | myVm1               |
    | Destino              |                     |
    | Selecionar uma máquina virtual |                     |
    | Máquina virtual          | myVm2               |
    | Porta                     | 22                  |

    ![Adicionar o Monitor de Ligação](./media/connection-monitor/add-connection-monitor.png)

## <a name="view-a-connection-monitor"></a>Ver um monitor de ligação

1. Execute os passos de 1 a 3 em [Criar um monitor de ligação](#create-a-connection-monitor) para ver a monitorização da ligação. É apresentada uma lista dos monitores de ligação existentes, conforme mostra a imagem seguinte:

    ![Monitores de ligação](./media/connection-monitor/connection-monitors.png)

2. Selecione o monitor com o nome **myVm1-myVm2(22)**, conforme mostra a imagem anterior, para ver detalhes do monitor, conforme mostra a imagem seguinte:

    ![Detalhes do monitor](./media/connection-monitor/vm-monitor.png)

    Tenha em atenção as seguintes informações:

    | Item                     | Valor                      | Detalhes                                                     |
    | ---------                | ---------                  |--------                                                     |
    | Estado                   | Acessível                  | Permite-lhe saber se o ponto final está acessível ou não.|
    | AVG. ROUND-TRIP          | Permite-lhe saber o tempo de ida e volta para estabelecer a ligação, em milissegundos. O monitor de ligação sonda a ligação a cada 60 segundos, para que possa monitorizar a latência ao longo do tempo.                                         |
    | Saltos                     | O monitor de ligação permite-lhe saber os saltos entre os dois pontos finais. Neste exemplo, a ligação é entre duas VMs na mesma rede virtual, pelo que existe apenas um salto, para o endereço IP 10.0.0.5. Se qualquer sistema existente ou rotas personalizadas encaminharem o tráfego entre as VMs através de um gateway VPN, ou aplicação virtual de rede, por exemplo, são listados saltos adicionais.                                                                                                                         |
    | ESTADO                   | As marcas de verificação verdes em cada ponto final informam que cada ponto final está em bom estado de funcionamento.    ||

## <a name="generate-alerts"></a>Gerar alertas

Os alertas são criados por regras de alerta no Azure Monitor e podem executar automaticamente consultas guardadas ou pesquisa de registos personalizadas em intervalos regulares. Um alerta gerado pode executar automaticamente uma ou mais ações, por exemplo, para notificar alguém ou iniciar outro processo. Ao definir uma regra de alerta, o recurso que segmenta determina a lista de métricas disponíveis que pode utilizar para gerar alertas.

1. No portal do Azure, selecione o serviço **Monitorizar** e, em seguida, selecione **Alertas** > **Nova regra de alerta**.
2. Clique em **Selecionar destino** e, em seguida, selecione os recursos que pretende alcançar. Selecione **Subscrição** e defina o **Tipo de recurso** para filtrar o Monitor de Ligação que pretende utilizar.

    ![ecrã de alerta com o destino selecionado](./media/connection-monitor/set-alert-rule.png)
1. Assim que tiver selecionado um recurso de destino, selecione **Adicionar critérios**. O Observador de Rede tem [métricas nas quais pode criar alertas](../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported). Defina os **Sinais disponíveis** para as métricas ProbesFailedPercent e AverageRoundtripMs:

    ![página de alerta com sinais selecionados](./media/connection-monitor/set-alert-signals.png)
1. Preencha os detalhes do alerta, como o nome da regra de alerta, a descrição e a gravidade. Também pode adicionar um grupo de ação ao alerta para automatizar e personalizar a resposta de alerta.

## <a name="view-a-problem"></a>Ver um problema

Por predefinição, o Azure permite a comunicação através de todas as portas entre VMs na mesma rede virtual. Ao longo do tempo, o utilizador ou alguém na sua organização poderá substituir as regras predefinidas do Azure, provocando inadvertidamente uma falha de comunicação. Execute os passos seguintes para criar um problema de comunicação e, em seguida, visualizar novamente o monitor de ligação:

1. Na caixa de pesquisa que se encontra na parte superior do portal, introduza *myResourceGroup*. Quando o grupo de recursos **myResourceGroup** for apresentado nos resultados da pesquisa, selecione-o.
2. Selecione o grupo de segurança de rede **myVm2-nsg**.
3. Selecione **Regras de segurança de entrada** e, em seguida, selecione **Adicionar**, conforme mostra a imagem seguinte:

    ![Regras de segurança de entrada](./media/connection-monitor/inbound-security-rules.png)

4. A regra predefinida que permite a comunicação entre todas as VMs numa rede virtual é a regra com o nome **AllowVnetInBound**. Crie uma regra com uma prioridade mais alta (número mais baixo) que a regra **AllowVnetInBound** que nega a comunicação de entrada através da porta 22. Selecione ou introduza as seguintes informações, aceite as predefinições restantes e selecione **Adicionar**:

    | Definição                 | Valor          |
    | ---                     | ---            |
    | Intervalos de portas de destino | 22             |
    | Ação                  | Negar           |
    | Prioridade                | 100            |
    | Nome                    | DenySshInbound |

5. Como o monitor de ligação sonda em intervalos de 60 segundos, aguarde alguns minutos e, em seguida, no lado esquerdo do portal, selecione **Observador de Rede**, **Monitor de ligação** e, em seguida, selecione novamente o monitor **myVm1-myVm2(22)**. Os resultados são diferentes agora, conforme mostra a imagem seguinte:

    ![Falha nos detalhes do monitor](./media/connection-monitor/vm-monitor-fault.png)

    Pode ver que há um ícone de exclamação vermelho na coluna de estado relativa à interface de rede **myvm2529**.

6. Para saber por que motivo o estado mudou, selecione 10.0.0.5, na imagem anterior. O monitor de ligação informa que o motivo da falha de comunicação é: *Tráfego bloqueado devido à seguinte regra do grupo de segurança de rede: UserRule_DenySshInbound*.

    Se não sabia que alguém tinha implementado a regra de segurança que criou no passo 4, saberia através do monitor de ligação que a regra está a causar o problema de comunicação. Em seguida, poderia alterar, substituir ou remover a regra, para restaurar a comunicação entre as VMs.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos e todos os recursos contidos no mesmo:

1. Introduza *myResourceGroup* na caixa **Pesquisar**, na parte superior do portal. Quando vir o **myResourceGroup** nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Introduza *myResourceGroup* em **ESCREVER O NOME DO GRUPO DE RECURSOS:** e selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a monitorizar uma ligação entre duas VMs. Aprendeu que uma regra do grupo de segurança de rede impediu a comunicação para uma VM. Para saber mais sobre todas as diferentes respostas que o monitor de ligação pode devolver, veja [tipos de resposta](network-watcher-connectivity-overview.md#response). Também pode monitorizar uma ligação entre uma VM, um nome de domínio completamente qualificado, um identificador de recurso uniforme ou um endereço IP.

A determinada altura, pode constatar que os recursos numa rede virtual não conseguem comunicar com os recursos noutras redes ligadas por um gateway de rede virtual do Azure. Avance para o próximo tutorial para aprender a diagnosticar um problema num gateway de rede virtual.

> [!div class="nextstepaction"]
> [Diagnosticar problemas de comunicação entre redes](diagnose-communication-problem-between-networks.md)