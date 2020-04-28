---
title: Recuperação de desastres utilizando DNS Azure e Gestor de Tráfego Microsoft Docs
description: Visão geral das soluções de recuperação de desastres utilizando o DNS Azure e o Gestor de Tráfego.
services: dns
documentationcenter: na
author: KumudD
manager: twooley
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
ms.openlocfilehash: 6eab1803bf5adab42be87b5f8567682c6d75947e
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74483539"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Recuperação após desastre com o DNS do Azure e o Gestor de Tráfego

A recuperação de desastres centra-se na recuperação de uma grave perda da funcionalidade de aplicação. Para escolher uma solução de recuperação de desastres, os proprietários de negócios e tecnologia devem primeiro determinar o nível de funcionalidade que é necessário durante uma catástrofe, tais como - indisponível, parcialmente disponível através de funcionalidade reduzida, ou disponibilidade retardada, ou totalmente disponível.
A maioria dos clientes empresariais está a escolher uma arquitetura multi-região para resiliência contra uma falha de nível de aplicação ou de infraestrutura. Os clientes podem escolher várias abordagens na busca de obter falhas e alta disponibilidade através de arquitetura redundante. Aqui estão algumas das abordagens populares:

- **Ativamente passiva com espera fria**: Nesta solução de failover, os VMs e outros aparelhos que estão a funcionar na região de espera não estão ativos até que haja necessidade de falhas. No entanto, o ambiente de produção é replicado sob a forma de backups, imagens VM ou modelos de Gestor de Recursos, para uma região diferente. Este mecanismo de failover é rentável, mas leva mais tempo para empreender uma falha completa.
 
    ![Ativo/Passivo com espera fria](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    *Figura - Ativa/Passiva com configuração de recuperação de desastres de espera a frio*

- **Ativo/Passivo com luz piloto**: Nesta solução de failover, o ambiente de espera é configurado com uma configuração mínima. A configuração tem apenas os serviços necessários em execução para suportar apenas um conjunto mínimo e crítico de aplicações. Na sua forma nativa, este cenário só pode executar a funcionalidade mínima, mas pode aumentar e gerar serviços adicionais para tomar a maior parte da carga de produção se ocorrer uma falha.
    
    ![Ativo/Passivo com luz piloto](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *Figura: Ativo/Passivo com configuração piloto de recuperação de desastres ligeiros*

- **Ativa/Passiva com standby quente**: Nesta solução de failover, a região de espera é pré-aquecida e está pronta para carregar a carga base, a escala automática é ligada, e todas as instâncias estão em funcionamento. Esta solução não é dimensionada para tomar toda a carga de produção, mas é funcional, e todos os serviços estão em funcionamento. Esta solução é uma versão aumentada da abordagem da luz piloto.
    
    ![Ativo/Passivo com espera quente](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    *Figura: Ativo/Passivo com configuração de recuperação de desastres de prontidão quente*
    
Para saber mais sobre o failover e a alta disponibilidade, consulte A Recuperação de [Desastres para Aplicações Azure](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).


## <a name="planning-your-disaster-recovery-architecture"></a>Planejando a sua arquitetura de recuperação de desastres

Existem dois aspetos técnicos para a criação da sua arquitetura de recuperação de desastres:
-  Utilizando um mecanismo de implementação para replicar instâncias, dados e configurações entre ambientes primários e de espera. Este tipo de recuperação de desastres pode ser feito de forma nativa através do Azure Site-Recovery através de eletrodomésticos/serviços parceiros da Microsoft Azure, como veritas ou NetApp. 
- Desenvolvendo uma solução para desviar o tráfego de rede/web do site principal para o site de espera. Este tipo de recuperação de desastres pode ser alcançado através do Azure DNS, do Azure Traffic Manager (DNS) ou dos equilibradores globais de carga de terceiros.

Este artigo limita-se a abordagens através da rede e da reorientação do tráfego web. Para obter instruções para configurar a recuperação do site Azure, consulte a Documentação de [Recuperação do Local do Azure](https://docs.microsoft.com/azure/site-recovery/).
O DNS é um dos mecanismos mais eficientes para desviar o tráfego da rede porque o DNS é muitas vezes global e externo para o centro de dados e está isolado de quaisquer falhas de nível de zona de disponibilidade regional ou de disponibilidade (AZ). Pode-se utilizar um mecanismo de failover baseado em DNS e em Azure, dois serviços DNS podem realizar o mesmo de alguma forma - Azure DNS (DNS autoritário) e Azure Traffic Manager (encaminhamento inteligente de tráfego baseado em DNS). 

É importante compreender poucos conceitos em DNS que são extensivamente utilizados para discutir as soluções fornecidas neste artigo:
- **DNS A Record** – A Records são ponteiros que apontam um domínio para um endereço IPv4. 
- **CNAME ou nome canónico** - Este tipo de gravação é usado para apontar para outro registo DNS. O CNAME não responde com um endereço IP, mas sim o ponteiro para o registo que contém o endereço IP. 
- **Encaminhamento Ponderado** – pode-se optar por associar um peso aos pontos finais de serviço e, em seguida, distribuir o tráfego com base nos pesos atribuídos. Este método de encaminhamento é um dos quatro mecanismos de encaminhamento de tráfego disponíveis no Traffic Manager. Para obter mais informações, consulte o método de [encaminhamento ponderado](../traffic-manager/traffic-manager-routing-methods.md#weighted).
- **Priority Routing** – O encaminhamento prioritário baseia-se em controlos de saúde dos pontos finais. Por padrão, o gestor de tráfego azure envia todo o tráfego para o ponto final de maior prioridade, e após uma falha ou desastre, o Traffic Manager encaminha o tráfego para o ponto final secundário. Para mais informações, consulte o método de [encaminhamento prioritário](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method).

## <a name="manual-failover-using-azure-dns"></a>Falha manual utilizando DNS Azure
A solução de failover manual Azure DNS para recuperação de desastres utiliza o mecanismo padrão dNS para falhar no local de backup. A opção manual via Azure DNS funciona melhor quando utilizada em conjunto com o standby frio ou a aproximação da luz piloto. 

![Falha manual utilizando DNS Azure](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

*Figura - Falha manual utilizando DNS Azure*

Os pressupostos assumidos para a solução são:
- Tanto os pontos finais primários como secundários têm IPs estáticos que não mudam frequentemente. Digamos que para o local primário o IP é 100.168.124.44 e o IP para o local secundário é 100.168.124,43.
- Existe uma zona DeND Azure para o local primário e secundário. Digamos que para o local principal o ponto final é prod.contoso.com e para o site de reserva é dr.contoso.com. Existe também um registo DNS\.para a aplicação principal conhecida como www contoso.com.   
- O TTL está na ou abaixo do RTO SLA definido na organização. Por exemplo, se uma empresa define o RTO da resposta a desastres de aplicação para 60 minutos, então o TTL deve ser inferior a 60 minutos, de preferência quanto mais baixo melhor. 
  Pode configurar o Azure DNS para uma falha manual da seguinte forma:
- Criar uma zona DNS
- Criar registos de zona DNS
- Atualizar registo CNAME

### <a name="step-1-create-a-dns"></a>Passo 1: Criar um DNS
Criar uma zona DNS (por exemplo, www\.contoso.com) como mostrado abaixo:

![Criar uma zona DNS em Azure](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

*Figura - Criar uma zona DNS em Azure*

### <a name="step-2-create-dns-zone-records"></a>Passo 2: Criar registos de zona DNS

Dentro desta zona, criem três\.registos (por exemplo - www contoso.com, prod.contoso.com e dr.consoto.com) como mostra abaixo.

![Criar registos de zona DNS](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

*Figura - Criar registos de zona DNS em Azure*

Neste cenário, o\.site, www contoso.com tem um TTL de 30 minutos, que fica muito abaixo do RTO declarado, e está a apontar para o local de produção prod.contoso.com. Esta configuração é durante operações comerciais normais. O TTL de prod.contoso.com e dr.contoso.com foi definido para 300 segundos ou 5 minutos. Pode utilizar um serviço de monitorização Azure, como o Azure Monitor ou o Azure App Insights, ou, qualquer soluçõe de monitorização de parceiros, como o Dynatrace, pode até utilizar soluções caseiras que possam monitorizar ou detetar falhas de aplicação ou de nível de infraestrutura virtual.

### <a name="step-3-update-the-cname-record"></a>Passo 3: Atualizar o registo CNAME

Uma vez detetada a falha, altere o valor recorde para apontar para dr.contoso.com como mostrado abaixo:
       
![Atualizar registo CNAME](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

*Figura - Atualizar o registo CNAME em Azure*

Dentro de 30 minutos, durante os quais a maioria dos\.resolvers irá refrescar o ficheiro da zona em cache, qualquer consulta a www contoso.com será redirecionada para dr.contoso.com.
Também pode executar o seguinte comando Azure CLI para alterar o valor CNAME:
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
Este passo pode ser executado manualmente ou através da automatização. Pode ser feito manualmente através da consola ou pelo Azure CLI. O SDK e a API Azure podem ser utilizados para automatizar a atualização CNAME para que não seja necessária qualquer intervenção manual. A automatização pode ser construída através de funções Azure ou dentro de uma aplicação de monitorização de terceiros ou mesmo a partir de instalações.

### <a name="how-manual-failover-works-using-azure-dns"></a>Como funciona a falha manual utilizando o Azure DNS
Uma vez que o servidor DNS está fora da zona de falha ou de desastre, é isolado contra qualquer tempo de inatividade. Isto permite ao utilizador arquiteto um cenário simples de failover que é rentável e funcionará todo o tempo assumindo que o operador tem conectividade de rede durante o desastre e pode fazer a reviravolta. Se a solução for escrita, então deve-se garantir que o servidor ou o serviço que executa o script devem ser isolados contra o problema que afeta o ambiente de produção. Além disso, tenha em mente o Baixo TTL que foi colocado contra a zona para que nenhum resolver em todo o mundo mantenha o ponto final em cache por muito tempo e os clientes possam aceder ao site dentro da RTO. Para uma luz de espera e piloto frio, uma vez que pode ser necessária alguma atividade pré-aperto e outra suinicultura – deve-se também dar tempo suficiente antes de fazer a viragem.

## <a name="automatic-failover-using-azure-traffic-manager"></a>Falha automática utilizando o Gestor de Tráfego Azure
Quando se tem arquiteturas complexas e múltiplos conjuntos de recursos capazes de desempenhar a mesma função, pode configurar o Gestor de Tráfego Azure (com base no DNS) para verificar a saúde dos seus recursos e encaminhar o tráfego do recurso não saudável para o recurso saudável. No exemplo seguinte, tanto a região primária como a região secundária têm uma implantação completa. Esta implementação inclui os serviços de nuvem e uma base de dados sincronizada. 

![Falha automática utilizando o Gestor de Tráfego Azure](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

*Figura - Falha automática utilizando o Gestor de Tráfego Azure*

No entanto, apenas a região primária está a lidar ativamente com os pedidos de rede dos utilizadores. A região secundária só se torna ativa quando a região primária sofre uma perturbação de serviço. Nesse caso, todas as novas redes solicitam a rota para a região secundária. Uma vez que a cópia de segurança da base de dados é quase instantânea, ambos os equilibradores de carga têm IPs que podem ser verificados na saúde, e os casos estão sempre a funcionar, esta topologia fornece uma opção para entrar para um RTO baixo e failover sem qualquer intervenção manual. A região secundária de failover deve estar pronta para entrar em direto imediatamente após o fracasso da região primária.
Este cenário é ideal para a utilização do Gestor de Tráfego Azure que tem sondas incorporadas para vários tipos de verificações de saúde, incluindo http/https e TCP. O gestor de tráfego azure também tem um motor de regra que pode ser configurado para falhar quando uma falha ocorre como descrito abaixo. Vamos considerar a seguinte solução usando o Traffic Manager:
- O cliente tem a Região #1 ponto final conhecido como prod.contoso.com com um IP estático como 100.168.124,44 e uma região #2 ponto final conhecido como dr.contoso.com com um IP estático como 100.168.124,43. 
-   Cada um destes ambientes é encabeçado através de uma propriedade virada para o público como um equilibrista de carga. O equilibrante de carga pode ser configurado para ter um ponto final baseado em DNS ou um nome de domínio totalmente qualificado (FQDN) como mostrado acima.
-   Todos os casos na Região 2 estão em quase replicação em tempo real com a Região 1. Além disso, as imagens da máquina estão atualizadas, e todos os dados de software/configuração são corrigidos e estão em linha com a Região 1.  
-   A autoscalcificação é pré-configurada com antecedência. 

As medidas tomadas para configurar a falha com o Gestor de Tráfego Azure são as seguintes:
1. Criar um novo perfil do Gestor de Tráfego Azure
2. Criar pontos finais dentro do perfil do Gestor de Tráfego
3. Configurar a configuração de verificação de saúde e failover

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>Passo 1: Criar um novo perfil do Gestor de Tráfego Azure
Crie um novo perfil de gestor de tráfego Azure com o nome contoso123 e selecione o método de Encaminhamento como Prioridade. Se tiver um grupo de recursos pré-existente com o que pretende associar, então pode selecionar um grupo de recursos existente, caso contrário, criar um novo grupo de recursos.

![Criar perfil de Gestor de Tráfego](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)

*Figura - Criar um perfil de Gestor de Tráfego*

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>Passo 2: Criar pontos finais dentro do perfil do Gestor de Tráfego

Neste passo, cria-se pontos finais que apontam para os locais de produção e recuperação de desastres. Aqui, escolha o **Tipo** como um ponto final externo, mas se o recurso estiver hospedado em Azure, então também pode escolher o **ponto final do Azure.** Se escolher o **ponto final do Azure,** então selecione um **recurso Target** que seja um Serviço de **Aplicações** ou um **IP público** que seja atribuído pelo Azure. A prioridade está definida como **1,** uma vez que é o serviço primário para a Região 1.
Da mesma forma, crie o ponto final de recuperação de desastres dentro do Traffic Manager também.

![Criar pontos finais de recuperação de desastres](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

*Figura - Criar pontos finais de recuperação de desastres*

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>Passo 3: Configurar a configuração de verificação de saúde e de failover

Neste passo, você definiu o DNS TTL para 10 segundos, que é homenageado pela maioria dos resolvers recursivos virados para a Internet. Esta configuração significa que nenhum resolver DNS cache a informação por mais de 10 segundos. Para as definições de monitor de ponto final, o caminho está atualmente definido em /ou raiz, mas pode personalizar as definições de ponto final para avaliar um caminho, por exemplo, prod.contoso.com/index. O exemplo abaixo mostra o **https** como o protocolo de sondagem. No entanto, também pode escolher **http** ou **tcp.** A escolha do protocolo depende da aplicação final. O intervalo de sondagem é definido para 10 segundos, o que permite uma sondagem rápida, e a repetição está definida para 3. Como resultado, o Gestor de Tráfego falhará até ao segundo ponto final se três intervalos consecutivos registarem uma falha. A fórmula que se segue define o tempo total para uma falha automatizada: Tempo para failover = TTL + Retry * Intervalo de sondagem E neste caso, o valor é de 10 + 3 * 10 = 40 segundos (Max).
Se o Retry estiver definido para 1 e o TTL estiver definido para 10 segundos, então o tempo para falhar mais de 10 + 1 * 10 = 20 segundos. Desloque o Retry a um valor superior a **1** para eliminar as hipóteses de falhas devido a falsos positivos ou a quaisquer pequenos blips de rede. 


![Configurar um exame de saúde](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

*Figura - Configurar a configuração de verificação de saúde e failover*

### <a name="how-automatic-failover-works-using-traffic-manager"></a>Como funciona a falha automática usando o Traffic Manager

Durante um desastre, o ponto final primário é sondado e o estado muda para **degradar-se** e o local de recuperação de desastres permanece **online**. Por predefinição, o Gestor de Tráfego envia todo o tráfego para o ponto final primário (com a prioridade mais alta). Se o ponto final principal parecer degradado, o Traffic Manager encaminha o tráfego para o segundo ponto final, desde que se mantenha saudável. Uma delas tem a opção de configurar mais pontos finais dentro do Traffic Manager que podem servir como pontos finais de failover adicionais, ou, como equilibradores de carga que partilham a carga entre pontos finais.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [o Gestor de Tráfego Azure.](../traffic-manager/traffic-manager-overview.md)
- Saiba mais sobre [o Azure DNS.](../dns/dns-overview.md)









