---
title: Implantar um balanceador de carga voltado para a Internet com o modelo IPv6-Azure
titleSuffix: Azure Load Balancer
description: Saiba como implantar o suporte a IPv6 para VMs Azure Load Balancer e com balanceamento de carga usando um modelo do Azure.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, o Balanceador de carga do azure, pilha dupla, ip público, ipv6 nativo, móvel, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: eb9703a1944a650f41d76c05d79764f8bdf8cd52
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045443"
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Implantar uma solução de balanceador de carga voltada para a Internet com IPv6 usando um modelo

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [CLI do Azure](load-balancer-ipv6-internet-cli.md)
> * [Modelo](load-balancer-ipv6-internet-template.md)


>[!NOTE] 
>Este artigo descreve um recurso IPv6 introdutório para permitir que os balanceadores de carga básicos forneçam conectividade IPv4 e IPv6. A conectividade IPv6 abrangente agora está disponível com o [IPv6 para o Azure VNETs](../virtual-network/ipv6-overview.md) , que integra a conectividade IPv6 com suas redes virtuais e inclui recursos importantes, como regras de grupo de segurança de rede IPv6, roteamento definido pelo usuário IPv6, balanceamento de carga básico e padrão e muito mais.  O IPv6 para Azure VNETs é o padrão recomendado para aplicativos IPv6 no Azure. Consulte [IPv6 para implantação do PowerShell de VNET do Azure](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)  

Um balanceador de carga do Azure é um balanceador de carga de Camada 4 (TCP, UDP). O balanceador de carga fornece elevada disponibilidade, ao distribuir o tráfego de entrada entre instâncias de serviço com bom estado de funcionamento nos serviços cloud ou máquinas virtuais num conjunto de balanceador de carga. O Balanceador de Carga do Azure pode também apresentar esses serviços em várias portas, vários endereços IP ou ambos.

## <a name="example-deployment-scenario"></a>Cenário de implementação de exemplo

O diagrama a seguir ilustra a solução de balanceamento de carga que está sendo implantada usando o modelo de exemplo descrito neste artigo.

![Cenário do Balanceador de carga](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

Nesse cenário, você criará os seguintes recursos do Azure:

* uma interface de rede virtual para cada VM com endereços IPv4 e IPv6 atribuídos
* um Load Balancer voltado para a Internet com um IPv4 e um endereço IP público IPv6
* duas regras de balanceamento de carga para mapear os VIPs públicos para os pontos de extremidade privados
* um conjunto de disponibilidade que contém as duas VMs
* duas VMs (máquinas virtuais)

## <a name="deploying-the-template-using-the-azure-portal"></a>Implantando o modelo usando o portal do Azure

Este artigo faz referência a um modelo que é publicado na Galeria de [modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) . Você pode baixar o modelo da galeria ou iniciar a implantação no Azure diretamente da galeria. Este artigo pressupõe que você baixou o modelo para o computador local.

1. Abra o portal do Azure e entre com uma conta que tenha permissões para criar VMs e recursos de rede em uma assinatura do Azure. Além disso, a menos que você esteja usando recursos existentes, a conta precisa de permissão para criar um grupo de recursos e uma conta de armazenamento.
2. Clique em "+ novo" no menu e digite "modelo" na caixa de pesquisa. Selecione "Implantação de modelo" nos resultados da pesquisa.

    ![lb-ipv6-portal-step2](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. Na folha tudo, clique em "Implantação de modelo".

    ![lb-ipv6-portal-step3](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Clique em "criar".

    ![lb-ipv6-portal-step4](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Clique em "Editar modelo". Exclua o conteúdo existente e copie/cole todo o conteúdo do arquivo de modelo (para incluir o início e término {}) e clique em "salvar".

    > [!NOTE]
    > Se você estiver usando o Microsoft Internet Explorer, ao colar, receberá uma caixa de diálogo solicitando que você permita o acesso à área de transferência do Windows. Clique em "permitir acesso".

    ![lb-ipv6-portal-step5](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Clique em "editar parâmetros". Na folha parâmetros, especifique os valores de acordo com as diretrizes na seção parâmetros do modelo e clique em "salvar" para fechar a folha parâmetros. Na folha implantação personalizada, selecione sua assinatura, um grupo de recursos existente ou crie uma. Se você estiver criando um grupo de recursos, selecione um local para o grupo de recursos. Em seguida, clique em **termos legais**e clique em **comprar** para obter os termos legais. O Azure começa a implantar os recursos. Leva vários minutos para implantar todos os recursos.

    ![lb-ipv6-portal-step6](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Para obter mais informações sobre esses parâmetros, consulte a seção [parâmetros e variáveis de modelo](#template-parameters-and-variables) mais adiante neste artigo.

7. Para ver os recursos criados pelo modelo, clique em procurar, role para baixo na lista até ver "grupos de recursos" e clique nele.

    ![lb-ipv6-portal-step7](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. Na folha grupos de recursos, clique no nome do grupo de recursos especificado na etapa 6. Você verá uma lista de todos os recursos que foram implantados. Se tudo correr bem, ele deverá dizer "êxito" em "última implantação". Caso contrário, verifique se a conta que você está usando tem permissões para criar os recursos necessários.

    ![lb-ipv6-portal-step8](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > Se você procurar os grupos de recursos imediatamente após concluir a etapa 6, a "última implantação" exibirá o status "Implantando" enquanto os recursos estão sendo implantados.

9. Clique em "myIPv6PublicIP" na lista de recursos. Você verá que ele tem um endereço IPv6 em endereço IP e que seu nome DNS é o valor especificado para o parâmetro dnsNameforIPv6LbIP na etapa 6. Esse recurso é o endereço IPv6 público e o nome do host que podem ser acessados por clientes da Internet.

    ![lb-ipv6-portal-step9](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Validar conectividade

Quando o modelo tiver sido implantado com êxito, você poderá validar a conectividade concluindo as seguintes tarefas:

1. Entre no portal do Azure e conecte-se a cada uma das VMs criadas pela implantação do modelo. Se você implantou uma VM do Windows Server, execute ipconfig/all em um prompt de comando. Você verá que as VMs têm endereços IPv4 e IPv6. Se você implantou VMs do Linux, precisará configurar o sistema operacional Linux para receber endereços IPv6 dinâmicos usando as instruções fornecidas para sua distribuição do Linux.
2. A partir de um cliente conectado à Internet IPv6, inicie uma conexão com o endereço IPv6 público do balanceador de carga. Para confirmar que o balanceador de carga está equilibrando entre as duas VMs, você pode instalar um servidor Web como o Microsoft Serviços de Informações da Internet (IIS) em cada uma das VMs. A página da Web padrão em cada servidor pode conter o texto "Server0" ou "Server1" para identificá-lo exclusivamente. Em seguida, abra um navegador da Internet em um cliente conectado à Internet IPv6 e navegue até o nome do host especificado para o parâmetro dnsNameforIPv6LbIP do balanceador de carga para confirmar a conectividade IPv6 de ponta a ponta para cada VM. Se você só vê a página da Web de um servidor, talvez seja necessário limpar o cache do navegador. Abra várias sessões de navegação privada. Você deve ver uma resposta de cada servidor.
3. A partir de um cliente conectado à Internet IPv4, inicie uma conexão com o endereço IPv4 público do balanceador de carga. Para confirmar se o balanceador de carga está equilibrando a carga das duas VMs, você pode testar usando o IIS, conforme detalhado na etapa 2.
4. De cada VM, inicie uma conexão de saída para um dispositivo de Internet conectado a IPv6 ou IPv4. Em ambos os casos, o IP de origem visto pelo dispositivo de destino é o endereço IPv4 ou IPv6 público do balanceador de carga.

> [!NOTE]
> O ICMP para IPv4 e IPv6 é bloqueado na rede do Azure. Como resultado, as ferramentas ICMP, como ping, sempre falham. Para testar a conectividade, use uma alternativa de TCP como TCPing ou o cmdlet Test-NetConnection do PowerShell. Observe que os endereços IP mostrados no diagrama são exemplos de valores que você pode ver. Como os endereços IPv6 são atribuídos dinamicamente, os endereços recebidos serão diferentes e poderão variar de acordo com a região. Além disso, é comum que o endereço IPv6 público no balanceador de carga inicie com um prefixo diferente dos endereços IPv6 privados no pool de back-ends.

## <a name="template-parameters-and-variables"></a>Parâmetros e variáveis de modelo

Um modelo de Azure Resource Manager contém várias variáveis e parâmetros que você pode personalizar para suas necessidades. As variáveis são usadas para valores fixos que você não deseja que um usuário altere. Os parâmetros são usados para os valores que você deseja que um usuário forneça ao implantar o modelo. O modelo de exemplo está configurado para o cenário descrito neste artigo. Você pode personalizar isso para as necessidades do seu ambiente.

O modelo de exemplo usado neste artigo inclui as seguintes variáveis e parâmetros:

| Parâmetro/variável | Notas |
| --- | --- |
| adminUsername |Especifique o nome da conta de administrador usada para entrar nas máquinas virtuais com. |
| adminPassword |Especifique a senha da conta de administrador usada para entrar nas máquinas virtuais com. |
| dnsNameforIPv4LbIP |Especifique o nome do host DNS que você deseja atribuir como o nome público do balanceador de carga. Esse nome é resolvido para o endereço IPv4 público do balanceador de carga. O nome deve estar em minúsculas e corresponder ao Regex: ^ [a-z] [a-Z0-9-]{1,61}[a-Z0-9] $. |
| dnsNameforIPv6LbIP |Especifique o nome do host DNS que você deseja atribuir como o nome público do balanceador de carga. Esse nome é resolvido para o endereço IPv6 público do balanceador de carga. O nome deve estar em minúsculas e corresponder ao Regex: ^ [a-z] [a-Z0-9-]{1,61}[a-Z0-9] $. Pode ser o mesmo nome do endereço IPv4. Quando um cliente envia uma consulta DNS para esse nome, o Azure retornará os registros a e AAAA quando o nome for compartilhado. |
| vmNamePrefix |Especifique o prefixo do nome da VM. O modelo acrescenta um número (0, 1, etc.) ao nome quando as VMs são criadas. |
| nicNamePrefix |Especifique o prefixo do nome da interface de rede. O modelo acrescenta um número (0, 1, etc.) ao nome quando as interfaces de rede são criadas. |
| storageAccountName |Insira o nome de uma conta de armazenamento existente ou especifique o nome de uma nova para ser criada pelo modelo. |
| availabilitySetName |Insira o nome do conjunto de disponibilidade a ser usado com as VMs |
| addressPrefix |O prefixo de endereço usado para definir o intervalo de endereços da rede virtual |
| subnetName |O nome da sub-rede em criada para a VNet |
| subnetPrefix |O prefixo de endereço usado para definir o intervalo de endereços da sub-rede |
| vnetName |Especifique o nome para a VNet usada pelas VMs. |
| ipv4PrivateIPAddressType |O método de alocação usado para o endereço IP privado (estático ou dinâmico) |
| ipv6PrivateIPAddressType |O método de alocação usado para o endereço IP privado (dinâmico). O IPv6 dá suporte apenas à alocação dinâmica. |
| numberOfInstances |O número de instâncias com balanceamento de carga implantadas pelo modelo |
| ipv4PublicIPAddressName |Especifique o nome DNS que você deseja usar para se comunicar com o endereço IPv4 público do balanceador de carga. |
| ipv4PublicIPAddressType |O método de alocação usado para o endereço IP público (estático ou dinâmico) |
| Ipv6PublicIPAddressName |Especifique o nome DNS que você deseja usar para se comunicar com o endereço IPv6 público do balanceador de carga. |
| ipv6PublicIPAddressType |O método de alocação usado para o endereço IP público (dinâmico). O IPv6 dá suporte apenas à alocação dinâmica. |
| lbName |Especifique o nome do balanceador de carga. Esse nome é exibido no portal ou usado ao se referir a ele com uma CLI ou um comando do PowerShell. |

As variáveis restantes no modelo contêm valores derivados que são atribuídos quando o Azure cria os recursos. Não altere essas variáveis.

## <a name="next-steps"></a>Passos seguintes

Para obter a sintaxe JSON e as propriedades de um balanceador de carga em um modelo, consulte [Microsoft. Network/Load Balancers](/azure/templates/microsoft.network/loadbalancers).
