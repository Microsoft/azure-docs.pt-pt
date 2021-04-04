---
title: Implementar um equilibrador de carga virado para a Internet com o modelo IPv6 - Azure
titleSuffix: Azure Load Balancer
description: Aprenda a implantar suporte IPv6 para Azure Load Balancer e VMs equilibrados de carga usando um modelo Azure.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, balançador de carga azul, pilha dupla, ip público, ipv6 nativo, móvel, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 415c95a441ac0cc6ed2dbf2d6a37f57d7a9e7341
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90562524"
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Implementar uma solução de balanceador de carga virada para a Internet com o IPv6 utilizando um modelo

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [CLI do Azure](load-balancer-ipv6-internet-cli.md)
> * [Modelo](load-balancer-ipv6-internet-template.md)


>[!NOTE] 
>Este artigo descreve uma funcionalidade introdutória do IPv6 para permitir que os Balanceadores de Carga Básica forneçam conectividade IPv4 e IPv6. A conectividade abrangente do IPv6 está agora disponível com [o IPv6 para VNETs Azure,](../virtual-network/ipv6-overview.md) que integra a conectividade IPv6 com as suas Redes Virtuais e inclui funcionalidades-chave como as regras do Grupo de Segurança da Rede IPv6, encaminhamento definido pelo utilizador IPv6, equilíbrio de carga básica e padrão IPv6, e muito mais.  IPv6 para Azure VNETs é a norma recomendada para aplicações IPv6 em Azure. Ver [IPv6 para implementação de Powershell Azure VNET](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)  

Um balanceador de carga do Azure é um balanceador de carga de Camada 4 (TCP, UDP). O balanceador de carga fornece elevada disponibilidade, ao distribuir o tráfego de entrada entre instâncias de serviço com bom estado de funcionamento nos serviços cloud ou máquinas virtuais num conjunto de balanceador de carga. O Balanceador de Carga do Azure pode também apresentar esses serviços em várias portas, vários endereços IP ou ambos.

## <a name="example-deployment-scenario"></a>Cenário de implantação de exemplo

O diagrama seguinte ilustra a solução de equilíbrio de carga que está a ser implementada utilizando o modelo de exemplo descrito neste artigo.

![O diagrama mostra um cenário de exemplo usado neste artigo, incluindo um cliente de estação de trabalho ligado a um Equilibr de Carga Azure através da Internet, ligado por sua vez a duas máquinas virtuais.](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

Neste cenário, criará os seguintes recursos Azure:

* uma interface de rede virtual para cada VM com endereços IPv4 e IPv6 atribuídos
* um Balanceador de Carga virado para a Internet com um IPv4 e um endereço IP público IPv6
* duas regras de equilíbrio de carga para mapear os VIPs públicos para os pontos finais privados
* um Conjunto de Disponibilidade que contém os dois VMs
* duas máquinas virtuais (VMs)

## <a name="deploying-the-template-using-the-azure-portal"></a>Implantação do modelo utilizando o portal Azure

Este artigo refere um modelo que é publicado na galeria [Azure Quickstart Templates.](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) Você pode baixar o modelo a partir da galeria ou lançar a implementação em Azure diretamente a partir da galeria. Este artigo pressupõe que você descarregou o modelo para o seu computador local.

1. Abra o portal Azure e inscreva-se com uma conta que tenha permissões para criar VMs e recursos de networking dentro de uma subscrição do Azure. Além disso, a menos que esteja a usar os recursos existentes, a conta precisa de permissão para criar um grupo de recursos e uma conta de armazenamento.
2. Clique em "+Novo" a partir do menu e escreva "modelo" na caixa de pesquisa. Selecione "Implementação do modelo" a partir dos resultados da pesquisa.

    ![A screenshot mostra o portal Azure com a implementação de Novo e Modelo selecionado.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. Na lâmina De Tudo, clique em "Implementação do Modelo".

    ![Screenshot mostra implementação de modelo no Mercado.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Clique em "Criar".

    ![A screenshot mostra a descrição da implementação do modelo no Mercado.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Clique em "Editar modelo". Elimine os conteúdos existentes e copie/cole em todo o conteúdo do ficheiro do modelo (para incluir o início e o fim { }), em seguida, clique em "Guardar".

    > [!NOTE]
    > Se estiver a utilizar o Microsoft Internet Explorer, quando colou recebe uma caixa de diálogo pedindo-lhe que permita o acesso à área de transferência do Windows. Clique em "Permitir o acesso".

    ![A screenshot mostra o passo mais disparado de uma implementação personalizada, que é o modelo de edição.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Clique em "Editar parâmetros". Na lâmina parâmetros, especifique os valores por orientação na secção de parâmetros do modelo e, em seguida, clique em "Guardar" para fechar a lâmina de parâmetros. Na lâmina de implementação personalizada, selecione a sua subscrição, um grupo de recursos existente ou crie uma. Se estiver a criar um grupo de recursos, selecione uma localização para o grupo de recursos. Em seguida, clique em **Termos Legais** e, em seguida, clique em **Comprar** para os termos legais. O Azure começa a mobilizar os recursos. Leva vários minutos para mobilizar todos os recursos.

    ![A screenshot mostra os passos envolvidos na implementação personalizada, começando com a introdução dos valores dos parâmetros do modelo.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Para obter mais informações sobre estes parâmetros, consulte a secção [de parâmetros e variáveis do modelo](#template-parameters-and-variables) mais tarde neste artigo.

7. Para ver os recursos criados pelo modelo, clique em Navegar, percorra a lista até ver "Grupos de Recursos" e, em seguida, clique nele.

    ![O screenshot mostra o portal Azure com grupos de Navegação e Recursos selecionados.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. Na lâmina dos grupos de recursos, clique no nome do grupo de recursos especificado no passo 6. Vê-se uma lista de todos os recursos que foram mobilizados. Se tudo correu bem, deve dizer "Bem sucedido" em "Última implantação". Caso contrário, certifique-se de que a conta que está a usar tem permissões para criar os recursos necessários.

    ![A screenshot mostra o estado da última implementação de um grupo de recursos, neste exemplo, Conseguiu.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > Se navegar nos grupos de recursos imediatamente após completar o passo 6, "Última implementação" apresentará o estado de "Implantação" enquanto os recursos estão a ser utilizados.

9. Clique em "myIPv6PublicIP" na lista de recursos. Pode ver que tem um endereço IPv6 no endereço IP, e que o seu nome DNS é o valor especificado para o parâmetro DNSNameforIPv6LbIP no passo 6. Este recurso é o endereço público IPv6 e nome de anfitrião que é acessível aos clientes da Internet.

    ![A screenshot mostra o endereço público do IPv6.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Validar conectividade

Quando o modelo tiver sido implementado com sucesso, pode validar a conectividade completando as seguintes tarefas:

1. Inscreva-se no portal Azure e ligue-se a cada um dos VMs criados pela implementação do modelo. Se tiver implantado um VM do Servidor do Windows, execute ipconfig /tudo a partir de uma solicitação de comando. Os VM têm endereços IPv4 e IPv6. Se implementou os VMs Linux, tem de configurar o Sistema Operativo Linux para receber endereços IPv6 dinâmicos utilizando as instruções fornecidas para a sua distribuição Linux.
2. A partir de um cliente ligado à Internet IPv6, inicie uma ligação ao endereço público IPv6 do equilibrista de carga. Para confirmar que o balanceador de carga está a equilibrar-se entre os dois VMs, pode instalar um servidor web como o Microsoft Internet Information Services (IIS) em cada um dos VMs. A página web predefinido de cada servidor pode conter o texto "Server0" ou "Server1" para identificá-lo de forma única. Em seguida, abra um navegador de Internet num cliente ligado à Internet IPv6 e navegue no nome anfitrião especificado para o parâmetro dnsNameforIPv6LbIP do equilibrista de carga para confirmar a conectividade IPv6 de ponta a cada VM. Se só vir a página web de apenas um servidor, poderá ter de limpar a cache do seu navegador. Abra várias sessões de navegação privadas. Deve ver uma resposta de cada servidor.
3. A partir de um cliente ligado à Internet IPv4, inicie uma ligação ao endereço público IPv4 do equilibrista de carga. Para confirmar que o balançador de carga está a equilibrar os dois VMs, pode testar utilizando o IIS, conforme descrito no Passo 2.
4. A partir de cada VM, inicie uma ligação de saída a um dispositivo internet ligado ao IPv6 ou iPv4. Em ambos os casos, o IP de origem visto pelo dispositivo de destino é o endereço público IPv4 ou IPv6 do equilibrista de carga.

> [!NOTE]
> O ICMP para o IPv4 e o IPv6 está bloqueado na rede Azure. Como resultado, ferramentas ICMP como ping falham sempre. Para testar a conectividade, utilize uma alternativa TCP como a TCPing ou o Test-NetConnection cmdlet PowerShell. Note que os endereços IP apresentados no diagrama são exemplos de valores que pode ver. Uma vez que os endereços IPv6 são atribuídos dinamicamente, os endereços que receber serão diferentes e podem variar por região. Além disso, é comum que o endereço público IPv6 no equilibrista de carga comece com um prefixo diferente dos endereços privados do IPv6 na piscina traseira.

## <a name="template-parameters-and-variables"></a>Parâmetros e variáveis do modelo

Um modelo do Gestor de Recursos Azure contém múltiplas variáveis e parâmetros que pode personalizar às suas necessidades. São utilizadas variáveis para valores fixos que não pretende que um utilizador altere. Os parâmetros são utilizados para valores que pretende que um utilizador forneça ao implementar o modelo. O modelo de exemplo está configurado para o cenário descrito neste artigo. Pode personalizar isto às necessidades do seu ambiente.

O modelo de exemplo utilizado neste artigo inclui as seguintes variáveis e parâmetros:

| Parâmetro / Variável | Notas |
| --- | --- |
| adminUsername |Especifique o nome da conta de administração usada para iniciar scontabilidade nas máquinas virtuais com. |
| adminPassword |Especifique a palavra-passe para a conta de administração utilizada para iniciar scontabilidade nas máquinas virtuais com. |
| dnsNameforIPv4LbIP |Especifique o nome de anfitrião DNS que pretende atribuir como nome público do equilibrador de carga. Este nome resolve o endereço público IPv4 do balanceador de carga. O nome deve ser minúsculo e corresponder ao regex: ^[a-z][a-z0-9-] {1,61} [a-z0-9]$. |
| dnsNameforIPv6LbIP |Especifique o nome de anfitrião DNS que pretende atribuir como nome público do equilibrador de carga. Este nome resolve o endereço público do IPv6 do balanceador de carga. O nome deve ser minúsculo e corresponder ao regex: ^[a-z][a-z0-9-] {1,61} [a-z0-9]$. Este pode ser o mesmo nome que o endereço IPv4. Quando um cliente envia uma consulta de DNS para este nome, a Azure devolverá os registos A e AAAA quando o nome for partilhado. |
| vmNamePrefix |Especifique o prefixo do nome VM. O modelo anexa um número (0, 1, etc.) ao nome quando os VMs são criados. |
| nicNamePrefix |Especifique o prefixo do nome da interface de rede. O modelo anexa um número (0, 1, etc.) ao nome quando as interfaces de rede são criadas. |
| storageAccountName |Introduza o nome de uma conta de armazenamento existente ou especifique o nome de uma nova a ser criada pelo modelo. |
| disponibilidadeSetName |Insira então o nome do conjunto de disponibilidade a utilizar com os VMs |
| addressPrefix |O prefixo de endereço utilizado para definir o intervalo de endereços da Rede Virtual |
| subnetName |O nome da sub-rede criada para o VNet |
| subnetPrefix |O prefixo de endereço utilizado para definir o intervalo de endereços da sub-rede |
| vnetName |Especificar o nome do VNet utilizado pelos VMs. |
| ipv4PrivateIPAddressType |O método de atribuição utilizado para o endereço IP privado (Estático ou Dinâmico) |
| ipv6PrivateIPAddressType |O método de atribuição utilizado para o endereço IP privado (Dinâmico). O IPv6 só suporta a atribuição dinâmica. |
| númeroOfInstances |O número de casos equilibrados de carga implementados pelo modelo |
| ipv4PublicIPAddressName |Especifique o nome DNS que pretende utilizar para comunicar com o endereço IPv4 público do equilibrista de carga. |
| ipv4PublicIPAddressType |O método de atribuição utilizado para o endereço IP público (Estático ou Dinâmico) |
| Ipv6PublicIPAddressName |Especifique o nome DNS que pretende utilizar para comunicar com o endereço IPv6 público do esquilibrista de carga. |
| ipv6PublicIPAddressType |O método de atribuição utilizado para o endereço IP público (Dinâmico). O IPv6 só suporta a atribuição dinâmica. |
| lbName |Especificar o nome do esquilibrador de carga. Este nome é apresentado no portal ou utilizado quando se refere a ele com um comando CLI ou PowerShell. |

As variáveis restantes no modelo contêm valores derivados que são atribuídos quando Azure cria os recursos. Não mude estas variáveis.

## <a name="next-steps"></a>Passos seguintes

Para a sintaxe JSON e propriedades de um equilibrador de carga num modelo, consulte [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers).
