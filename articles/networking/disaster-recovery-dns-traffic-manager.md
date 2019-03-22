---
title: Recuperação após desastre com o DNS do Azure e o Gestor de tráfego | Documentos da Microsoft
description: Descrição geral das soluções de recuperação após desastre com o DNS do Azure e o Gestor de tráfego.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/08/2018
ms.author: kumud
ms.openlocfilehash: ec252c1f45e5c27f17b725f6ab68cc94f67897c4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120742"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Recuperação após desastre com o DNS do Azure e o Gestor de Tráfego

Recuperação após desastre se concentra em recuperar de uma grave perda de funcionalidade do aplicativo. Para escolher uma solução de recuperação após desastre, os proprietários de negócios e tecnologia devem primeiro determinar o nível de funcionalidade que é necessário durante um desastre, por exemplo, -, indisponível, parcialmente disponível por meio de funcionalidade reduzida ou da disponibilidade atrasada, ou estão totalmente disponíveis.
A maioria dos clientes de empresas estão optando uma arquitetura de várias regiões para resiliência contra um aplicativo ou a ativação pós-falha ao nível de infraestrutura. Os clientes podem escolher várias abordagens em meu questionamento para alcançar a ativação pós-falha e a elevada disponibilidade através de arquitetura redundante. Aqui estão algumas das abordagens populares:

- **Modo ativo-passivo com reserva progressiva**: Nesta solução de ativação pós-falha, as VMs e outros dispositivos em execução na região em modo de espera não estão ativos até que haja uma necessidade de ativação pós-falha. No entanto, o ambiente de produção é replicado na forma de cópias de segurança, de imagens VM ou modelos do Resource Manager, para uma região diferente. Esse mecanismo de ativação pós-falha é económico, mas demora mais tempo para realizar uma ativação pós-falha concluída.
 
    ![Ativo/passivo com reserva progressiva](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    *Figura - ativo/passivo com a configuração da recuperação após desastre de reserva dinâmica frio*

- **Ativo/passivo com piloto light**: Nesta solução de ativação pós-falha, o ambiente em espera é configurado com uma configuração mínima. A configuração tem apenas os serviços necessários para suportar apenas um mínimo e crítico conjunto de aplicações a executar. No seu formato nativo, este cenário pode apenas executar a funcionalidade mínima, mas pode aumentar verticalmente e gerar serviços adicionais para tomar medidas em volume da carga de produção, se ocorrer uma ativação pós-falha.
    
    ![Ativo/passivo com light piloto](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *Figura: Ativo/passivo com a configuração de recuperação após desastre de luz do piloto*

- **Ativo/passivo com reserva quente**: Nesta solução de ativação pós-falha, a região em modo de espera é previamente warmed e está pronta para a carga de base, dimensionamento automático está ativado e todas as instâncias estão em execução. Esta solução não é dimensionada para assumir a carga de produção total, mas está funcional e todos os serviços estão em execução. Esta solução é uma versão aumentada da abordagem de luz do piloto.
    
    ![Ativo/passivo com reserva quente](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    *Figura: Ativo/passivo com a configuração da recuperação após desastre de reserva quente*
    
Para saber mais sobre a ativação pós-falha e a elevada disponibilidade, veja [recuperação após desastre para aplicações do Azure](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).


## <a name="planning-your-disaster-recovery-architecture"></a>Planear a sua arquitetura de recuperação após desastre

Existem dois aspetos técnicos para configurar a sua arquitetura de recuperação após desastre:
-  Usando um mecanismo de implantação para replicar as instâncias, dados e configurações entre ambientes de principais e de reserva. Este tipo de recuperação após desastre pode ser feito nativamente através do Azure-recuperação de sites através do Microsoft Azure aplicações/serviços de parceiros, como o Veritas ou NetApp. 
- Desenvolvimento de uma solução para desviar o tráfego de rede/web a partir do site primário para o site em modo de espera. Este tipo de recuperação após desastre pode ser obtido através de DNS do Azure, Manager(DNS) de tráfego do Azure ou balanceadores de carga global de terceiros.

Este artigo está limitado a abordagens através do redirecionamento de tráfego de rede e a Web. Para obter instruções para configurar a recuperação de Site do Azure, consulte [documentação do Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).
O DNS é um dos mecanismos mais eficientes para desviar o tráfego de rede, como o DNS é, muitas vezes, globais e externos para o Centro de dados e é isolado da quaisquer falhas regionais ou disponibilidade de nível de zona (AZ). É possível usar um mecanismo de ativação pós-falha com base no DNS e no Azure, dois serviços DNS podem levar a cabo o mesmo, de alguma forma - DNS do Azure (DNS autoritativos) e o Gestor de tráfego do Azure (com base em DNS inteligente o encaminhamento de tráfego). 

É importante compreender alguns conceitos no DNS que são utilizados extensivamente para discutir as soluções fornecidas neste artigo:
- **Registo DNS** – registos são ponteiros que apontam um domínio para um endereço IPv4. 
- **Nome CNAME ou Canonical** -este tipo de registo é utilizado para apontar para outro registo DNS. CNAME não responde com um endereço IP, mas em vez disso, o ponteiro para o registo que contém o endereço IP. 
- **Ponderada encaminhamento** – um pode optar por associar um peso para pontos finais de serviço e, em seguida, distribuir o tráfego com base nos pesos atribuídos. Este método de encaminhamento é um dos quatro tráfego encaminhamento mecanismos disponíveis no Gestor de tráfego. Para obter mais informações, consulte [ponderada de método de encaminhamento](../traffic-manager/traffic-manager-routing-methods.md#weighted).
- **Encaminhamento de prioridade** – encaminhamento de prioridade é baseado em verificações de estado de funcionamento dos pontos de extremidade. Por predefinição, o Gestor de tráfego do Azure envia o tráfego para o ponto de final de prioridade mais elevado e, após uma falha ou desastre, o Gestor de tráfego encaminha o tráfego para o ponto final secundário. Para obter mais informações, consulte [método de encaminhamento prioritário](../traffic-manager/traffic-manager-routing-methods.md#priority).

## <a name="manual-failover-using-azure-dns"></a>Ativação pós-falha manual através de DNS do Azure
A solução de ativação pós-falha manual do DNS do Azure para recuperação após desastre utiliza o mecanismo DNS padrão a ativação pós-falha para o site de cópia de segurança. A opção manual através do DNS do Azure funciona melhor quando usado em conjunto com o modo de espera frio ou a abordagem de luz do piloto. 

![Ativação pós-falha manual através de DNS do Azure](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

*Figura - ativação pós-falha Manual através de DNS do Azure*

As suposições feitas para a solução são:
- Pontos de extremidade primários e secundários têm IPs estáticos que não são alterados frequentemente. Digamos que, para o site primário, o IP é 100.168.124.44 e o IP para o site secundário é 100.168.124.43.
- Existe uma zona DNS do Azure para o site primário e secundário. Digamos para o site primário, o ponto final é prod.contoso.com e para o site de cópia de segurança é dr.contoso.com. Um registo DNS para a aplicação principal, conhecido como www\.contoso.com também existe.   
- O valor de TTL é igual ou abaixo do SLA RTO definido na organização. Por exemplo, se uma empresa define o RTO da resposta de desastres de aplicativo a ser 60 minutos, em seguida, o valor de TTL deve ser inferior a 60 minutos, de preferência a inferior melhor. 
  Pode configurar DNS do Azure para ativação pós-falha manual da seguinte forma:
- Criar uma zona DNS
- Criar registos de zona DNS
- Atualizar o registo CNAME

### <a name="step-1-create-a-dns"></a>Passo 1: Criar um DNS
Criar uma zona DNS (por exemplo, www\.contoso.com) conforme mostrado abaixo:

![Criar uma zona DNS no Azure](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

*Figura - criar uma zona DNS no Azure*

### <a name="step-2-create-dns-zone-records"></a>Passo 2: Criar registos de zona DNS

Dentro desta zona criar registos de três (por exemplo, www\.contoso.com, prod.contoso.com e dr.consoto.com) como mostrados abaixo.

![Criar registos de zona DNS](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

*Figura - criar registos de zona DNS no Azure*

Este cenário, o site, o www\.contoso.com tem um valor de TTL 30 minutos, que é bem abaixo o RTO declarado e está a apontar para o prod.contoso.com de site de produção. Esta configuração é durante operações comerciais normais. O valor de TTL de prod.contoso.com e dr.contoso.com foi definida como 300 segundos ou 5 minutos. Pode utilizar um serviço, como o Azure Monitor ou o Azure App Insights de monitorização do Azure ou qualquer parceiro de soluções, como a Dynatrace de monitorização, ainda pode utilizar as soluções aumenta domésticas que podem monitorar ou detetar a aplicação ou falhas de nível de infra-estrutura virtual.

### <a name="step-3-update-the-cname-record"></a>Passo 3: Atualizar o registo CNAME

Depois de detetada falha, altere o valor de registo para apontar para dr.contoso.com, conforme mostrado abaixo:
       
![Atualizar o registo CNAME](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

*Figura - atualizar o registo CNAME no Azure*

Dentro de 30 minutos, durante o qual a maioria das resoluções atualizará o ficheiro de zona em cache, de qualquer consulta para www\.contoso.com será redirecionado para dr.contoso.com.
Também pode executar o seguinte comando da CLI do Azure para alterar o valor CNAME:
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
Este passo pode ser executado manualmente ou por meio da automação. Ele pode ser feito manualmente por meio do console ou pela CLI do Azure. O SDK do Azure e a API podem ser utilizados para automatizar a atualização CNAME para que é necessária nenhuma intervenção manual. Automatização pode ser criada através das funções do Azure ou dentro de um aplicativo de monitoramento de terceiros ou mesmo a partir de locais.

### <a name="how-manual-failover-works-using-azure-dns"></a>Manual de como funciona a ativação pós-falha com o DNS do Azure
Uma vez que o servidor DNS está fora da zona de ativação pós-falha ou desastre, é isolado em relação a qualquer período de inatividade. Isso permite que o usuário projetar um cenário de ativação pós-falha simples que seja econômico e funcionará todos no tempo, supondo que o operador tem conectividade de rede durante a desastres e pode fazer o flip. Se a solução é convertido em script, em seguida, um tem de garantir que o servidor ou serviço a executar o script deve ser isolado contra o problema que afetam o ambiente de produção. Além disso, tenha em atenção o valor de TTL baixa que foi definido em relação a zona, para que não existem resolvedor em todo o mundo mantém o ponto final em cache de longa duração e os clientes podem aceder a sites dentro o RTO. Para um progressiva e piloto luz, uma vez que alguns prewarming e de outras atividades administrativas podem ser necessários – um também deve ser dada tempo suficiente antes de fazer o flip.

## <a name="automatic-failover-using-azure-traffic-manager"></a>Ativação pós-falha automática utilizando o Gestor de tráfego do Azure
Quando tiver arquiteturas complexas e de vários conjuntos de recursos capazes de executar a mesma função, pode configurar o Gestor de tráfego do Azure (com base no DNS) para verificar o estado de funcionamento dos seus recursos e encaminhar o tráfego do recurso de mau para o bom estado de funcionamento recurso. No exemplo seguinte, a região primária e a região secundária tem uma implementação completa. Esta implementação inclui os serviços cloud e uma base de dados sincronizada. 

![Ativação pós-falha automática utilizando o Gestor de tráfego do Azure](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

*Figura - ativação pós-falha automática utilizando o Gestor de tráfego do Azure*

No entanto, apenas a região primária está ativamente a processar pedidos de rede dos utilizadores. A região secundária é ativada apenas quando a região primária sofre uma interrupção do serviço. Nesse caso, todos os novos pedidos de rede encaminham para a região secundária. Desde a cópia de segurança da base de dados é quase instantânea, os balanceadores de carga ter IPs que pode ser a opção estiver marcado, de estado de funcionamento e as instâncias são sempre se e em execução, essa topologia fornece uma opção para em fica pronto para um RTO baixo e a ativação pós-falha sem qualquer intervenção manual. A região de ativação pós-falha secundário tem de estar pronta para go-live imediatamente após a falha da região primária.
Este cenário é ideal para a utilização do Azure Gestor de tráfego que tem sondas incorporadas para vários tipos de verificações de estado de funcionamento, incluindo http / https e TCP. O Gestor de tráfego do Azure também tem um motor de regras que pode ser configurado para ativação pós-falha quando ocorre uma falha, conforme descrito abaixo. Vamos considerar a seguinte solução utilizando o Gestor de tráfego:
- O cliente tem o ponto de extremidade de região n. º 1, conhecido como prod.contoso.com com um IP estático como 100.168.124.44 e um ponto de extremidade de região n. º 2, conhecido como dr.contoso.com com um IP estático como 100.168.124.43. 
-   Cada um desses ambientes é apoiada por meio de uma propriedade destinado ao pública, como um balanceador de carga. O Balanceador de carga pode ser configurado para ter um ponto de extremidade com base no DNS ou um nome de domínio completamente qualificado (FQDN), conforme mostrado acima.
-   Todas as instâncias na região 2 estão em perto de replicação em tempo real com 1 da região. Além disso, as imagens de máquina estão atualizadas e todos os dados de configuração do software é corrigido e estão de acordo com a 1 de região.  
-   Dimensionamento automático é pré-configurado com antecedência. 

As medidas tomadas para configurar a ativação pós-falha com o Gestor de tráfego do Azure são os seguintes:
1. Criar um novo perfil do Gestor de tráfego do Azure
2. Criar pontos finais no perfil do Gestor de tráfego
3. Definir a configuração de verificação e ativação pós-falha do Estado de funcionamento

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>Passo 1: Criar um novo perfil do Gestor de tráfego do Azure
Criar um novo perfil do Gestor de tráfego do Azure com o nome contoso123 e selecione o método de encaminhamento como prioridade. Se tiver um grupo de recursos já existente que pretende associar, em seguida, pode selecionar um grupo de recursos existente, caso contrário, crie um novo grupo de recursos.

![Criar perfil do Gestor de tráfego](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)
*figura - criar um perfil do Gestor de tráfego*

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>Passo 2: Criar pontos finais no perfil do Gestor de tráfego

Neste passo, vai criar pontos finais que apontam para a produção e locais de recuperação após desastre. Aqui, escolher a **tipo** como um ponto final externo, mas se o recurso está alojado no Azure, em seguida, pode escolher **ponto final do Azure** também. Se optar **ponto final do Azure**, em seguida, selecione um **recurso de destino** ou seja, seja um **serviço de aplicações** ou um **IP público** que foi alocado pelo Azure. A prioridade é definida como **1** , uma vez que é o serviço principal para a região 1.
Da mesma forma, crie o desastre recuperação ponto final dentro do Gestor de tráfego também.

![Criar pontos finais de recuperação de desastres](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

*Figura - criar pontos finais de recuperação de desastres*

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>Passo 3: Definir a configuração de verificação e ativação pós-falha do Estado de funcionamento

Neste passo, definir o valor de TTL de DNS para 10 segundos, que é honradas pela maioria das resoluções de recursiva de acesso à internet. Esta configuração significa que nenhum resolvedor DNS colocarão em cache as informações de mais de 10 segundos. Para as definições do monitor de ponto final, o caminho é o conjunto atual em / ou de raiz, mas pode personalizar as definições de ponto final para avaliar um caminho, por exemplo, prod.contoso.com/index. O exemplo abaixo mostra a **https** como o protocolo de pesquisa. No entanto, pode escolher **http** ou **tcp** também. A escolha do protocolo depende a aplicação de end. O intervalo de pesquisa está definido para 10 segundos, que permite a pesquisa rápida e a repetição é definida como 3. Como resultado, o Gestor de tráfego efetuará a ativação pós-falha para o segundo ponto de extremidade se uma falha de se registar três intervalos consecutivos. A fórmula seguinte define o tempo total de failover automático: Hora de ativação pós-falha = TTL + Repita * intervalo de Probing e nesse caso, o valor é 10 + 3 * 10 = 40 segundos (máx.).
Se a repetição está definida como 1 e o TTL é definida para 10 segundos, em seguida, o tempo de ativação pós-falha 10 + 1 * 10 = 20 segundos. Defina a repetição para um valor maior do que **1** para eliminar as possibilidades de ativações pós-falha devido a falsos positivos ou qualquer blips de rede secundárias. 


![Configurar a verificação de estado de funcionamento](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

*Figura – configure a configuração de verificação e ativação pós-falha do Estado de funcionamento*

### <a name="how-automatic-failover-works-using-traffic-manager"></a>Automática como funciona a ativação pós-falha utilizando o Gestor de tráfego

Durante um desastre, o ponto final primário obtém sondado e o estado muda para **degradado** e o site de recuperação após desastre permanece **Online**. Por predefinição, o Gestor de tráfego envia o tráfego para o ponto final (prioridade mais alta) primário. Se o ponto final primário aparecer degradado, o Gestor de tráfego encaminha o tráfego para o segundo ponto de extremidade, desde que ele permaneça em bom estado. Um tem a opção para configurar mais pontos finais no Traffic Manager que podem servir como pontos finais de ativação pós-falha adicionais ou, como balanceadores de carga entre os pontos finais de partilha de carga.

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [Gestor de tráfego do Azure](../traffic-manager/traffic-manager-overview.md).
- Saiba mais sobre [DNS do Azure](../dns/dns-overview.md).









