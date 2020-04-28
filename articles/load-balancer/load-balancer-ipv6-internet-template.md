---
title: Implemente um equilibrador de carga virado para a Internet com o modelo IPv6 - Azure
titleSuffix: Azure Load Balancer
description: Aprenda a implementar suporte IPv6 para O Equilíbrio de Carga Azure e VMs equilibrados com carga usando um modelo Azure.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, equilibrador de carga azul, dupla pilha, ip público, ipv6 nativo, móvel, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: eb9703a1944a650f41d76c05d79764f8bdf8cd52
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76045443"
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Implemente uma solução de equilíbrio de carga virada para a Internet com o IPv6 utilizando um modelo

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [CLI do Azure](load-balancer-ipv6-internet-cli.md)
> * [Modelo](load-balancer-ipv6-internet-template.md)


>[!NOTE] 
>Este artigo descreve uma funcionalidade introdutória do IPv6 para permitir que os Equilibradores básicos de carga forneçam conectividade IPv4 e IPv6. A conectividade IPv6 abrangente está agora disponível com [o IPv6 para VNETs Azure](../virtual-network/ipv6-overview.md) que integra a conectividade IPv6 com as suas Redes Virtuais e inclui funcionalidades-chave como as regras do IPv6 Network Security Group, o encaminhamento definido pelo utilizador IPv6, o equilíbrio de carga Básico e Standard IPv6, e muito mais.  IPv6 para VNETs Azure é o padrão recomendado para aplicações IPv6 em Azure. Consulte o IPv6 para a implantação da [Powershell Azure VNET](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)  

Um balanceador de carga do Azure é um balanceador de carga de Camada 4 (TCP, UDP). O balanceador de carga fornece elevada disponibilidade, ao distribuir o tráfego de entrada entre instâncias de serviço com bom estado de funcionamento nos serviços cloud ou máquinas virtuais num conjunto de balanceador de carga. O Balanceador de Carga do Azure pode também apresentar esses serviços em várias portas, vários endereços IP ou ambos.

## <a name="example-deployment-scenario"></a>Cenário de implementação de exemplo

O diagrama seguinte ilustra a solução de equilíbrio de carga que está a ser implementada utilizando o modelo de exemplo descrito neste artigo.

![Cenário do Balanceador de carga](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

Neste cenário irá criar os seguintes recursos Azure:

* uma interface de rede virtual para cada VM com endereços IPv4 e IPv6 atribuídos
* um Balancer de carga virado para a Internet com um IPv4 e um endereço IP público IPv6
* duas regras de equilíbrio de carga para mapear os VIPs públicos para os pontos finais privados
* um Conjunto de Disponibilidade que contém os dois VMs
* duas máquinas virtuais (VMs)

## <a name="deploying-the-template-using-the-azure-portal"></a>Implantação do modelo utilizando o portal Azure

Este artigo refere um modelo publicado na galeria [De modelos Azure Quickstart.](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) Pode baixar o modelo da galeria ou lançar a implantação em Azure diretamente a partir da galeria. Este artigo assume que descarregou o modelo para o seu computador local.

1. Abra o portal Azure e inscreva-se com uma conta que tenha permissões para criar VMs e recursos de networking dentro de uma subscrição azure. Além disso, a menos que esteja a usar os recursos existentes, a conta precisa de permissão para criar um grupo de recursos e uma conta de armazenamento.
2. Clique em "+Novo" a partir do menu e escreva "modelo" na caixa de pesquisa. Selecione "Implementação do modelo" a partir dos resultados da pesquisa.

    ![lb-ipv6-portal-step2](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. Na lâmina tudo, clique em "Implementação do modelo".

    ![lb-ipv6-portal-step3](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Clique em "Criar".

    ![lb-ipv6-portal-step4](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Clique em "Editar modelo". Elimine os conteúdos existentes e copie/cole em todo o conteúdo do ficheiro do modelo (para incluir o início e a extremidade { }), clique em "Guardar".

    > [!NOTE]
    > Se estiver a utilizar o Microsoft Internet Explorer, quando colar recebe uma caixa de diálogo a pedir-lhe que permita o acesso à área de sobre-saque do Windows. Clique em "Permitir o acesso".

    ![lb-ipv6-portal-step5](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Clique em "Editar parâmetros". Na lâmina parâmetros, especifique os valores de acordo com a orientação na secção de parâmetros do modelo e, em seguida, clique em "Guardar" para fechar a lâmina dos Parâmetros. Na lâmina de implementação personalizada, selecione a sua subscrição, um grupo de recursos existente ou crie uma. Se estiver a criar um grupo de recursos, então selecione um local para o grupo de recursos. Em seguida, clique em **termos legais**e, em seguida, clique em **Comprar** para os termos legais. O Azure começa a mobilizar os recursos. Leva vários minutos para mobilizar todos os recursos.

    ![lb-ipv6-portal-step6](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Para obter mais informações sobre estes parâmetros, consulte a secção de [parâmetros e variáveis do Modelo](#template-parameters-and-variables) mais tarde neste artigo.

7. Para ver os recursos criados pelo modelo, clique em Navegar, percorra a lista até ver "Grupos de Recursos", em seguida, clique nele.

    ![lb-ipv6-portal-step7](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. Na lâmina dos grupos de recursos, clique no nome do grupo de recursos especificado no passo 6. Vê uma lista de todos os recursos que foram mobilizados. Se tudo correr bem, deve dizer "Bem sucedido" em "Última implantação". Caso contrário, certifique-se de que a conta que está a usar tem permissões para criar os recursos necessários.

    ![lb-ipv6-portal-step8](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > Se navegar nos seus Grupos de Recursos imediatamente após completar o passo 6, a "Última implementação" mostrará o estado de "Implantação" enquanto os recursos estão a ser implementados.

9. Clique em "myIPv6PublicIP" na lista de recursos. Verifica que tem um endereço IPv6 no endereço IP e que o seu nome DNS é o valor especificado para o parâmetro dnsNameforIPv6LbIP no passo 6. Este recurso é o endereço público IPv6 e nome de anfitrião que é acessível aos clientes da Internet.

    ![lb-ipv6-portal-step9](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Validar a conectividade

Quando o modelo tiver sido implementado com sucesso, pode validar a conectividade completando as seguintes tarefas:

1. Inscreva-se no portal Azure e ligue-se a cada um dos VMs criados pela implementação do modelo. Se implementou um VM do Servidor windows, execute ipconfig /tudo a partir de um pedido de comando. Vê que os VMs têm endereços IPv4 e IPv6. Se implementou VMs Linux, precisa configurar o Sistema Linux OS para receber endereços IPv6 dinâmicos utilizando as instruções fornecidas para a sua distribuição Linux.
2. A partir de um cliente ligado à Internet IPv6, inicie uma ligação com o endereço iPv6 público do equilibrador de carga. Para confirmar que o equilibrador de carga está a equilibrar-se entre os dois VMs, pode instalar um servidor web como o Microsoft Internet Information Services (IIS) em cada um dos VMs. A página web predefinida em cada servidor poderia conter o texto "Server0" ou "Server1" para identificá-lo de forma única. Em seguida, abra um navegador de Internet num cliente ligado à Internet IPv6 e navegue para o nome de anfitrião especificado para o parâmetro dnsNameforIPv6LbIP do balancedor de carga para confirmar a conectividade IPv6 de ponta a ponta a cada VM. Se você só vir a página web de apenas um servidor, você pode precisar limpar o seu cache de navegador. Abra várias sessões de navegação privadas. Deve ver uma resposta de cada servidor.
3. A partir de um cliente ligado à Internet IPv4, inicie uma ligação com o endereço iPv4 público do equilibrador de carga. Para confirmar que o equilibrador de carga está a equilibrar os dois VMs, pode testar a utilização do IIS, conforme detalhado no Passo 2.
4. A partir de cada VM, inicie uma ligação de saída a um dispositivo de Internet iPv6 ou IPv4 ligado. Em ambos os casos, a fonte IP vista pelo dispositivo de destino é o endereço iPv4 ou IPv6 público do equilibrador de carga.

> [!NOTE]
> O ICMP tanto para iPv4 como IPv6 está bloqueado na rede Azure. Como resultado, ferramentas do ICMP como ping falham sempre. Para testar a conectividade, utilize uma alternativa TCP, como o TCPing ou o cmdlet PowerShell Test-NetConnection. Note que os endereços IP mostrados no diagrama são exemplos de valores que pode ver. Uma vez que os endereços IPv6 são atribuídos dinamicamente, os endereços que recebe diferirão e podem variar por região. Além disso, é comum que o endereço IPv6 público no equilibrista de carga comece com um prefixo diferente dos endereços iPv6 privados na piscina traseira.

## <a name="template-parameters-and-variables"></a>Parâmetros e variáveis do modelo

Um modelo de Gestor de Recursos Azure contém múltiplas variáveis e parâmetros que pode personalizar às suas necessidades. As variáveis são usadas para valores fixos que não pretende que um utilizador altere. Os parâmetros são usados para valores que pretende que um utilizador forneça ao implementar o modelo. O modelo de exemplo está configurado para o cenário descrito neste artigo. Pode personalizar isto às necessidades do seu ambiente.

O modelo de exemplo utilizado neste artigo inclui as seguintes variáveis e parâmetros:

| Parâmetro / Variável | Notas |
| --- | --- |
| adminUsername |Especifique o nome da conta de administração utilizada para iniciar sessão nas máquinas virtuais com. |
| adminPassword |Especifique a palavra-passe para a conta de administração utilizada para iniciar sessão nas máquinas virtuais com. |
| dnsNameforIPv4LbIP |Especifique o nome de anfitrião DNS que pretende atribuir como nome público do equilibrador de carga. Este nome resolve o endereço iPv4 público do equilibrador de carga. O nome deve ser minúsculo e corresponder ao regex: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$. |
| dnsNameforIPv6LbIP |Especifique o nome de anfitrião DNS que pretende atribuir como nome público do equilibrador de carga. Este nome resolve o endereço iPv6 público do equilibrador de carga. O nome deve ser minúsculo e corresponder ao regex: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$. Este pode ser o mesmo nome do endereço IPv4. Quando um cliente envia uma consulta dNS para este nome, o Azure devolverá os registos A e AAAA quando o nome for partilhado. |
| vmNamePrefix |Especifique o prefixo do nome VM. O modelo anexa um número (0, 1, etc.) ao nome quando os VMs são criados. |
| nicNamePrefix |Especifique o prefixo do nome da interface da rede. O modelo anexa um número (0, 1, etc.) ao nome quando as interfaces de rede são criadas. |
| storageAccountName |Introduza o nome de uma conta de armazenamento existente ou especifique o nome de uma nova a criar pelo modelo. |
| disponibilidadeSetName |Insira o nome então da disponibilidade definida para ser usada com os VMs |
| addressPrefix |O prefixo de endereço utilizado para definir a gama de endereços da Rede Virtual |
| subnome |O nome da subnet criada para o VNet |
| subnetPrefix |O prefixo de endereço utilizado para definir a gama de endereços da sub-rede |
| vnetName |Especifique o nome para o VNet utilizado pelos VMs. |
| ipv4PrivateIPAddressType |O método de atribuição utilizado para o endereço IP privado (Estático ou Dinâmico) |
| ipv6PrivateIPAddressType |O método de atribuição utilizado para o endereço IP privado (Dinâmico). O IPv6 apenas suporta a atribuição dinâmica. |
| númeroOfInstances |O número de instâncias equilibradas de carga implantadas pelo modelo |
| ipv4PublicIPAddressName |Especifique o nome DNS que pretende utilizar para comunicar com o endereço iPv4 público do equilibrante de carga. |
| ipv4PublicIPAddressType |O método de atribuição utilizado para o endereço IP público (Estático ou Dinâmico) |
| Ipv6PublicIPAddressName |Especifique o nome DNS que pretende utilizar para comunicar com o endereço IPv6 público do equilibrante de carga. |
| ipv6PublicIPAddressType |O método de atribuição utilizado para o endereço IP público (Dinâmico). O IPv6 apenas suporta a atribuição dinâmica. |
| lbName |Especifique o nome do equilibrador de carga. Este nome é apresentado no portal ou utilizado quando se refere a ele com um comando CLI ou PowerShell. |

As variáveis restantes no modelo contêm valores derivados que são atribuídos quando Azure cria os recursos. Não mude estas variáveis.

## <a name="next-steps"></a>Passos seguintes

Para a sintaxe JSON e propriedades de um equilibrador de carga num modelo, consulte [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers).
