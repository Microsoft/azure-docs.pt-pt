---
title: Implementar um balanceador de carga com acesso à Internet com IPv6 - modelo do Azure
titlesuffix: Azure Load Balancer
description: Como implementar o suporte de IPv6 para o Azure Load Balancer e as VMs com balanceamento de carga.
services: load-balancer
documentationcenter: na
author: KumudD
keywords: IPv6, o Balanceador de carga do azure, pilha dupla, ip público, ipv6 nativo, móvel, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 4a8c7309a07238ef3410e42c3d631ad525f023cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61216858"
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Implementar um acesso à Internet Balanceador de carga solução com o IPv6 com um modelo

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [CLI do Azure](load-balancer-ipv6-internet-cli.md)
> * [Modelo](load-balancer-ipv6-internet-template.md)



Um balanceador de carga do Azure é um balanceador de carga de Camada 4 (TCP, UDP). O balanceador de carga fornece elevada disponibilidade, ao distribuir o tráfego de entrada entre instâncias de serviço com bom estado de funcionamento nos serviços cloud ou máquinas virtuais num conjunto de balanceador de carga. O Balanceador de Carga do Azure pode também apresentar esses serviços em várias portas, vários endereços IP ou ambos.

## <a name="example-deployment-scenario"></a>Cenário de implementação de exemplo

O diagrama seguinte ilustra a solução de balanceamento de carga que está a ser implementada usando o modelo de exemplo descrito neste artigo.

![Cenário do Balanceador de carga](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

Neste cenário, irá criar os seguintes recursos do Azure:

* Uma interface de rede virtual para cada VM com endereços IPv4 e IPv6 atribuídos
* um balanceador de carga com acesso à Internet com um IPv4 e um endereço IP público IPv6
* Duas regras para mapear os VIPs públicos para os pontos de extremidade privados de balanceamento de carga
* um conjunto de disponibilidade que contém as duas VMs
* Duas máquinas virtuais (VMs)

## <a name="deploying-the-template-using-the-azure-portal"></a>Implementar o modelo com o portal do Azure

Este artigo faz referência a um modelo que está publicado na [modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) galeria. Pode transferir o modelo a partir da galeria ou iniciar a implementação no Azure diretamente a partir da galeria. Este artigo pressupõe que transferiu o modelo para o computador local.

1. Abra o portal do Azure e inicie sessão com uma conta que tenha permissões para criar VMs e recursos de rede dentro de uma subscrição do Azure. Além disso, a menos que estiver usando os recursos existentes, a conta necessita da permissão para criar um grupo de recursos e uma conta de armazenamento.
2. Clique em "+ novo" de menu em seguida, o tipo "modelo" na caixa de pesquisa. Selecione "Implementação do modelo" resultados da pesquisa.

    ![lb-ipv6-portal-step2](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. Em tudo, o painel, clique em "Implementação do modelo".

    ![lb-ipv6-portal-step3](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Clique em "Criar".

    ![lb-ipv6-portal-step4](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Clique em "Editar modelo". Elimine o conteúdo existente e copiar/colar em todo o conteúdo do ficheiro de modelo (para incluir o início e terminar com {}), em seguida, clique em "Salvar".

    > [!NOTE]
    > Se estiver a utilizar o Microsoft Internet Explorer, quando colar, receber uma caixa de diálogo a pedir-lhe para permitir o acesso para a área de transferência do Windows. Clique em "Permitir acesso".

    ![lb-ipv6-portal-step5](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Clique em "Editar parâmetros". No painel de parâmetros, especifique os valores com a orientação na secção de parâmetros de modelo, em seguida, clique em "Guardar" para fechar o painel de parâmetros. No painel implementação personalizada, selecione a sua subscrição, grupo de recursos existente ou criar uma. Se estiver a criar um grupo de recursos, em seguida, selecione uma localização para o grupo de recursos. Em seguida, clique em **termos legais**, em seguida, clique em **Compra** para os termos legais. Azure começa a implementar os recursos. Demora alguns minutos para implementar todos os recursos.

    ![lb-ipv6-portal-step6](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Para obter mais informações sobre estes parâmetros, consulte a [variáveis e parâmetros de modelo](#template-parameters-and-variables) seção mais adiante neste artigo.

7. Para ver os recursos criados pelo modelo, clique em Procurar, desloque para baixo a lista até ver "Grupos de recursos", em seguida, clique no mesmo.

    ![lb-ipv6-portal-step7](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. No painel de grupos de recursos, clique no nome do grupo de recursos que especificou no passo 6. É apresentada uma lista de todos os recursos que foram implementadas. Se tudo tenha corrido bem, ele deverá indicar "Com êxito" em "Última implementação." Se não estiver, certifique-se de que a conta que está a utilizar tem permissões para criar os recursos necessários.

    ![lb-ipv6-portal-step8](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > Se seus grupos de recursos que navega imediatamente depois de concluir o passo 6, "Última implementação" exibirá o status de "Implantar" enquanto os recursos estão a ser implementados.

9. Clique em "myIPv6PublicIP" na lista de recursos. Verá que ele tem um endereço de IPv6 em endereço IP e de que o respetivo nome DNS é o valor especificado para o parâmetro dnsNameforIPv6LbIP no passo 6. Este recurso é o público IPv6 endereço e nome do anfitrião que esteja acessível para os clientes de Internet.

    ![lb-ipv6-portal-step9](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Validar a conectividade

Quando o modelo foi implementado com êxito, pode validar a conectividade ao concluir as seguintes tarefas:

1. Inicie sessão no portal do Azure e estabelecer ligação com cada uma das VMs criadas pela implementação do modelo. Se implementou uma VM do Windows Server, execute ipconfig/tudo a partir de um prompt de comando. Verá que as VMs têm endereços IPv4 e IPv6. Se tiver implementado as VMs do Linux, tem de configurar o SO Linux para receber os endereços IPv6 dinâmicos com as instruções fornecidas para a distribuição de Linux.
2. De um cliente ligado à IPv6 Internet, inicie uma ligação para o endereço IPv6 público do Balanceador de carga. Para confirmar que o Balanceador de carga é o balanceamento entre as duas VMs, foi possível instalar um servidor web, como o Microsoft Internet Information Services (IIS) em cada uma das VMs. A página da web padrão em cada servidor pode conter o texto "Server0" ou "Servidor1" para identificar exclusivamente. Em seguida, abra um browser num cliente ligado à IPv6 Internet e navegue para o nome de anfitrião especificado para o parâmetro de dnsNameforIPv6LbIP do Balanceador de carga para confirmar a conectividade IPv6 de ponta a ponta para cada VM. Se vir apenas a página da web de apenas um servidor, terá de limpar a cache do browser. Abra várias sessões de navegação privadas. Deverá ver uma resposta de cada servidor.
3. De um cliente ligado à IPv4 Internet, inicie uma ligação para o endereço IPv4 público do Balanceador de carga. Para confirmar que o Balanceador de carga é as duas VMs de balanceamento de carga, poderia testar usando o IIS, conforme detalhado no passo 2.
4. A partir de cada VM, inicie uma ligação de saída para um dispositivo de IPv6 ou IPv4 ligados à Internet. Em ambos os casos, o IP de origem visto pelo dispositivo de destino é o endereço IPv4 ou IPv6 público do Balanceador de carga.

> [!NOTE]
> ICMP para IPv4 e IPv6 está bloqueada na rede do Azure. Como resultado, as ferramentas, como sempre ping do ICMP falharem. Para testar a conectividade, utilize uma alternativa TCP como TCPing ou o cmdlet Test-NetConnection do PowerShell. Tenha em atenção que os endereços IP mostrada no diagrama são exemplos de valores que poderá ver. Uma vez que os endereços IPv6 são atribuídos dinamicamente, os endereços que receberá serão diferentes e podem variar consoante a região. Além disso, é comum para o endereço IPv6 público no balanceador de carga para começar com um prefixo diferente do que os endereços IPv6 privados no conjunto de back-end.

## <a name="template-parameters-and-variables"></a>Parâmetros de modelo e as variáveis

Um modelo Azure Resource Manager contém várias variáveis e parâmetros que pode personalizar para as suas necessidades. As variáveis são usadas para valores fixos, que não pretende que um usuário altere. Parâmetros são usados para valores que pretende que um utilizador para fornecer ao implementar o modelo. O modelo de exemplo está configurado para o cenário descrito neste artigo. Pode personalizá-la às necessidades do seu ambiente.

O modelo de exemplo utilizado neste artigo inclui os seguintes parâmetros e variáveis:

| Parâmetro / Variable | Notas |
| --- | --- |
| adminUsername |Especifique o nome da conta de administrador utilizada para iniciar sessão para as máquinas virtuais com. |
| adminPassword |Especifique a palavra-passe para a conta de administrador utilizada para iniciar sessão para as máquinas virtuais com. |
| dnsNameforIPv4LbIP |Especifique o nome de anfitrião DNS que pretende atribuir como o nome público do Balanceador de carga. Este nome é resolvido para o endereço de IP IPv4 público do Balanceador de carga. O nome tem de estar em minúsculas e de corresponder ao regex: ^ [a-z] [uma-z0 - 9-]{1,61}[a-z0-9] $. |
| dnsNameforIPv6LbIP |Especifique o nome de anfitrião DNS que pretende atribuir como o nome público do Balanceador de carga. Este nome é resolvido para o endereço IPv6 público do Balanceador de carga. O nome tem de estar em minúsculas e de corresponder ao regex: ^ [a-z] [uma-z0 - 9-]{1,61}[a-z0-9] $. Isso pode ser o mesmo nome que o endereço IPv4. Quando um cliente envia uma consulta DNS para este nome de Azure irá devolver a e AAAA regista quando o nome é partilhado. |
| vmNamePrefix |Especifique o prefixo de nome VM. O modelo de anexa um número (0, 1, etc.) para o nome quando as VMs são criadas. |
| nicNamePrefix |Especifique o prefixo de nome de interface de rede. O modelo de anexa um número (0, 1, etc.) para o nome quando são criadas as interfaces de rede. |
| storageAccountName |Introduza o nome de uma conta de armazenamento existente ou especifique o nome de um novo a ser criado pelo modelo. |
| availabilitySetName |Em seguida, introduza o nome do conjunto de disponibilidade para ser utilizado com as VMs |
| addressPrefix |O prefixo de endereço usado para definir o intervalo de endereços da rede Virtual |
| subnetName |O nome da sub-rede em criado para a VNet |
| subnetPrefix |O prefixo de endereço usado para definir o intervalo de endereços da sub-rede |
| vnetName |Especifique o nome para a VNet utilizada pelas VMs. |
| ipv4PrivateIPAddressType |O método de alocação utilizado para o endereço IP privado (estático ou dinâmico) |
| ipv6PrivateIPAddressType |O método de alocação utilizado para o endereço IP privado (dinâmico). IPv6 só suporta a alocação dinâmica. |
| numberOfInstances |O número de instâncias com balanceamento de carga implementado pelo modelo |
| ipv4PublicIPAddressName |Especifique o nome DNS que pretende utilizar para comunicar com o endereço IPv4 público do Balanceador de carga. |
| ipv4PublicIPAddressType |O método de alocação utilizado para o endereço IP público (estáticos ou dinâmicos) |
| Ipv6PublicIPAddressName |Especifique o nome DNS que pretende utilizar para comunicar com o endereço IPv6 público do Balanceador de carga. |
| ipv6PublicIPAddressType |O método de alocação utilizado para o endereço IP público (dinâmico). IPv6 só suporta a alocação dinâmica. |
| lbName |Especifique o nome do Balanceador de carga. Este nome é apresentado no portal ou utilizado ao fazer referência a ele com um comando CLI ou o PowerShell. |

As variáveis restantes no modelo contenham valores derivados que são atribuídos quando o Azure cria os recursos. Não altere essas variáveis.

## <a name="next-steps"></a>Passos Seguintes

Para a sintaxe JSON e propriedades de um balanceador de carga num modelo, consulte [Network/loadbalancers](/azure/templates/microsoft.network/loadbalancers).
