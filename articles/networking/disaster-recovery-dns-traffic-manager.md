---
title: Recuperação de desastres usando Azure DNS e Traffic Manager | Microsoft Docs
description: Visão geral das soluções de recuperação de desastres utilizando o Azure DNS e o Traffic Manager.
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
ms.openlocfilehash: 8cb1a490ac8edf2630253b45d99c3394bbe721b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98234159"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Recuperação após desastre com o DNS do Azure e o Gestor de Tráfego

A recuperação de desastres centra-se na recuperação de uma grave perda de funcionalidade de aplicação. Para escolher uma solução de recuperação de desastres, os proprietários de empresas e tecnologia devem primeiro determinar o nível de funcionalidade que é necessário durante uma catástrofe, como - indisponível, parcialmente disponível através de funcionalidades reduzidas, ou disponibilidade retardada, ou totalmente disponível.
A maioria dos clientes empresariais está a escolher uma arquitetura multi-região para resiliência contra uma aplicação ou falha de nível de infraestrutura. Os clientes podem escolher várias abordagens na busca de alcançar o failover e a alta disponibilidade através de arquitetura redundante. Eis algumas das abordagens populares:

- **Ativamente passiva com atenção a frio**: Nesta solução de failover, os VMs e outros aparelhos que estão a funcionar na região de espera não estão ativos até que haja necessidade de falha. No entanto, o ambiente de produção é replicado sob a forma de backups, imagens VM ou modelos de Gestor de Recursos, para uma região diferente. Este mecanismo de failover é rentável, mas demora mais tempo a empreender um completo fracasso.
 
    ![Ativo/Passivo com espera fria](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    *Figura - Ativa/Passiva com configuração de recuperação de desastres em espera a frio*

- **Ativo/Passivo com luz piloto**: Nesta solução de failover, o ambiente de espera é configurado com uma configuração mínima. A configuração tem apenas os serviços necessários em execução para suportar apenas um conjunto mínimo e crítico de aplicações. Na sua forma nativa, este cenário só pode executar a funcionalidade mínima, mas pode aumentar e criar serviços adicionais para tomar a maior parte da carga de produção em caso de falha.
    
    ![Ativo/Passivo com luz piloto](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *Figura: Ativo/Passivo com configuração piloto de recuperação de desastres ligeiros*

- **Ativo/Passivo com standby quente**: Nesta solução de failover, a região de espera está pré-aquecida e está pronta para assumir a carga base, a escala automática é ligada e todas as ocorrências estão a funcionar. Esta solução não é dimensionada para levar a carga de produção completa, mas é funcional, e todos os serviços estão em funcionamento. Esta solução é uma versão aumentada da abordagem da luz piloto.
    
    ![Ativo/Passivo com standby quente](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    *Figura: Ativo/Passivo com configuração de recuperação de desastres em espera quente*
    
Para saber mais sobre falhas e alta disponibilidade, consulte [a Recuperação de Desastres para Aplicações Azure](/azure/architecture/resiliency/disaster-recovery-azure-applications).


## <a name="planning-your-disaster-recovery-architecture"></a>Planejando a sua arquitetura de recuperação de desastres

Existem dois aspetos técnicos para a criação da sua arquitetura de recuperação de desastres:
-  Utilizando um mecanismo de implantação para replicar casos, dados e configurações entre ambientes primários e de espera. Este tipo de recuperação de desastres pode ser feito de forma nativa através do Azure Site-Recovery através de eletrodomésticos parceiros da Microsoft Azure, como veritas ou NetApp. 
- Desenvolver uma solução para desviar o tráfego de rede/web do local primário para o local de espera. Este tipo de recuperação de desastres pode ser alcançado através de Azure DNS, Azure Traffic Manager (DNS), ou equilibradores globais de carga de terceiros.

Este artigo limita-se a abordagens através da reorientação de tráfego da Rede e da Web. Para obter instruções para configurar a recuperação do local de Azure, consulte [a documentação de recuperação do local do Azure](../site-recovery/index.yml).
O DNS é um dos mecanismos mais eficientes para desviar o tráfego da rede porque o DNS é muitas vezes global e externo ao centro de dados e é isolado de quaisquer falhas de nível regional ou de disponibilidade (AZ). Pode-se utilizar um mecanismo de failover baseado em DNS e em Azure, dois serviços DNS podem realizar o mesmo de alguma forma - Azure DNS (DNS autoritário) e Azure Traffic Manager (encaminhamento de tráfego inteligente baseado em DNS). 

É importante compreender poucos conceitos no DNS que são amplamente utilizados para discutir as soluções fornecidas neste artigo:
- **DNS A Record** – A Records são indicações que apontam um domínio para um endereço IPv4. 
- **NOME CNAME ou Canónico** - Este tipo de gravação é usado para apontar para outro registo DNS. O CNAME não responde com um endereço IP, mas sim o ponteiro para o registo que contém o endereço IP. 
- **Encaminhamento ponderado** – pode-se optar por associar um peso aos pontos finais de serviço e, em seguida, distribuir o tráfego com base nos pesos atribuídos. Este método de encaminhamento é um dos quatro mecanismos de encaminhamento de tráfego disponíveis no Gestor de Tráfego. Para obter mais informações, consulte [o método de encaminhamento ponderado](../traffic-manager/traffic-manager-routing-methods.md#weighted).
- **Encaminhamento prioritário** – O encaminhamento prioritário baseia-se em verificações de saúde dos pontos finais. Por defeito, o gestor de tráfego da Azure envia todo o tráfego para o ponto final prioritário mais elevado, e após uma falha ou desastre, o Gestor de Tráfego encaminha o tráfego para o ponto final secundário. Para obter mais informações, consulte [o método de encaminhamento prioritário](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method).

## <a name="manual-failover-using-azure-dns"></a>Falha manual usando DNS do Azure
A solução de falha manual Azure DNS para recuperação de desastres utiliza o mecanismo dNS padrão para falhar no local de backup. A opção manual via Azure DNS funciona melhor quando utilizada em conjunto com o modo de espera frio ou a abordagem da luz piloto. 

![Falha manual usando DNS do Azure](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

*Figura - Failover manual usando DNS Azure*

Os pressupostos feitos para a solução são:
- Ambos os pontos finais primários e secundários têm IPs estáticos que não mudam frequentemente. Para o site principal, o IP é 100.168.124.44 e o IP para o site secundário é 100.168.124.43.
- Existe uma zona Azure DNS para o local primário e secundário. Diga para o local principal que o ponto final é prod.contoso.com e para o site de reserva é dr.contoso.com. Existe também um registo DNS para a aplicação principal conhecida como www \. contoso.com.   
- O TTL está em ou abaixo do conjunto RTO SLA na organização. Por exemplo, se uma empresa definir o RTO da resposta de desastre de aplicação para 60 minutos, então o TTL deve ser inferior a 60 minutos, de preferência quanto mais baixo melhor. 
  Pode configurar o Azure DNS para o failover manual da seguinte forma:
- Criar uma zona DNS
- Criar registos de zonas DNS
- Atualizar o registo da CNAME

### <a name="step-1-create-a-dns"></a>Passo 1: Criar um DNS
Crie uma zona DE DNS (por exemplo, www \. contoso.com) como mostrado abaixo:

![Criar uma zona DE DNS em Azure](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

*Figura - Criar uma zona DE DNS em Azure*

### <a name="step-2-create-dns-zone-records"></a>Passo 2: Criar registos de zonas DNS

Dentro desta zona crie três registos (por exemplo - www \. contoso.com, prod.contoso.com e dr.consoto.com) como mostra abaixo.

![Criar registos de zonas DNS](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

*Figura - Criar registos de zonas DNS em Azure*

Neste cenário, o site, www \. contoso.com tem um TTL de 30 minutos, que está muito abaixo do RTO declarado, e aponta para o local de produção prod.contoso.com. Esta configuração é durante operações comerciais normais. O TTL de prod.contoso.com e dr.contoso.com foi programado para 300 segundos ou 5 minutos. Pode utilizar um serviço de monitorização Azure, como o Azure Monitor ou o Azure App Insights, ou, qualquer soluções de monitorização de parceiros como a Dynatrace, pode até usar soluções caseiras que possam monitorizar ou detetar falhas de aplicação ou de infraestrutura virtual.

### <a name="step-3-update-the-cname-record"></a>Passo 3: Atualizar o recorde da CNAME

Uma vez detetada a falha, altere o valor de registo para apontar para dr.contoso.com como mostrado abaixo:
       
![Atualizar o registo da CNAME](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

*Figura - Atualizar o recorde cname em Azure*

Dentro de 30 minutos, durante os quais a maioria dos resolvers irá refrescar o ficheiro da zona em cache, qualquer consulta para www \. contoso.com será redirecionada para dr.contoso.com.
Também pode executar o seguinte comando Azure CLI para alterar o valor CNAME:
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
Este passo pode ser executado manualmente ou através da automatização. Pode ser feito manualmente através da consola ou pelo Azure CLI. O Azure SDK e a API podem ser utilizados para automatizar a atualização CNAME de modo a que não seja necessária qualquer intervenção manual. A automatização pode ser construída através de funções Azure ou dentro de uma aplicação de monitorização de terceiros ou mesmo a partir de instalações.

### <a name="how-manual-failover-works-using-azure-dns"></a>Como funciona o failover manual usando o Azure DNS
Uma vez que o servidor DNS está fora da zona de failover ou de desastre, é isolado contra qualquer tempo de inatividade. Isto permite ao utilizador arquitetar um cenário simples de failover que é rentável e funcionará todo o tempo assumindo que o operador tem conectividade de rede durante o desastre e pode fazer o flip. Se a solução for scriptada, então deve-se garantir que o servidor ou serviço que executa o script deve ser isolado contra o problema que afeta o ambiente de produção. Além disso, tenha em mente o baixo TTL que foi definido contra a zona para que nenhum resolver em todo o mundo mantenha o ponto final em cache por muito tempo e os clientes podem aceder ao site dentro do RTO. Para uma luz fria e piloto, uma vez que pode ser necessária alguma atividade administrativa pré-aguerrida e outras atividades administrativas – deve-se também dar tempo suficiente antes de fazer o lançamento.

## <a name="automatic-failover-using-azure-traffic-manager"></a>Falha automática usando O Gestor de Tráfego Azure
Quando você tem arquiteturas complexas e múltiplos conjuntos de recursos capazes de executar a mesma função, você pode configurar Azure Traffic Manager (com base em DNS) para verificar a saúde dos seus recursos e encaminhar o tráfego do recurso não saudável para o recurso saudável. No exemplo seguinte, tanto a região primária como a região secundária têm uma implantação completa. Esta implementação inclui os serviços em nuvem e uma base de dados sincronizada. 

![Falha automática usando O Gestor de Tráfego Azure](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

*Figura - Falha automática usando O Gestor de Tráfego Azure*

No entanto, apenas a região primária está a lidar ativamente com os pedidos de rede dos utilizadores. A região secundária só se torna ativa quando a região primária sofre uma perturbação de serviço. Nesse caso, todas as novas redes solicitam a rota para a região secundária. Uma vez que a cópia de segurança da base de dados é quase instantânea, ambos os equilibradores de carga têm IPs que podem ser verificados na saúde, e as instâncias estão sempre em funcionamento, esta topologia oferece uma opção para entrar para um RTO baixo e falha sem qualquer intervenção manual. A região de falência secundária deve estar pronta para viver imediatamente após o fracasso da região primária.
Este cenário é ideal para a utilização do Azure Traffic Manager que tem sondas incorporadas para vários tipos de verificações de saúde, incluindo http/https e TCP. O gestor de tráfego Azure também tem um motor de regras que pode ser configurado para falhar quando ocorre uma falha como descrito abaixo. Vamos considerar a seguinte solução usando o Gestor de Tráfego:
- O cliente tem a Região #1 ponto final conhecido como prod.contoso.com com um IP estático como 100.168.124.44 e um ponto final da Região #2 conhecido como dr.contoso.com com um IP estático como 100.168.124.43. 
-   Cada um destes ambientes é frontal através de uma propriedade virada para o público como um equilibrador de carga. O equilibrador de carga pode ser configurado para ter um ponto final baseado em DNS ou um nome de domínio totalmente qualificado (FQDN) como mostrado acima.
-   Todos os casos na Região 2 estão em quase uma réplica em tempo real com a Região 1. Além disso, as imagens da máquina estão atualizadas e todos os dados de software/configuração são corrigidos e estão em conformidade com a Região 1.  
-   A autoscalagem é pré-configurada com antecedência. 

As medidas tomadas para configurar o failover com o Azure Traffic Manager são as seguintes:
1. Criar um novo perfil de Gestor de Tráfego Azure
2. Criar pontos finais dentro do perfil de Gestor de Tráfego
3. Configurar a verificação de saúde e a configuração de failover

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>Passo 1: Criar um novo perfil do Gestor de Tráfego Azure
Crie um novo perfil de gestor de tráfego Azure com o nome contoso123 e selecione o método de encaminhamento como Prioridade. Se tiver um grupo de recursos pré-existente com o quais pretende associar-se, então pode selecionar um grupo de recursos existente, caso contrário, criar um novo grupo de recursos.

![Criar perfil de Gestor de Tráfego](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)

*Figura - Criar um perfil de Gestor de Tráfego*

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>Passo 2: Criar pontos finais dentro do perfil do Gestor de Tráfego

Neste passo, cria-se pontos finais que apontam para os locais de produção e recuperação de desastres. Aqui, escolha o **Tipo** como um ponto final externo, mas se o recurso estiver hospedado no Azure, então também pode escolher **o ponto final do Azure.** Se escolher **o ponto final do Azure,** selecione um recurso **Target** que seja um Serviço **de Aplicações** ou um **IP público** que seja atribuído pela Azure. A prioridade é definida como **1,** uma vez que é o serviço primário para a Região 1.
Da mesma forma, crie também o ponto final de recuperação de desastres dentro do Traffic Manager.

![Criar pontos finais de recuperação de desastres](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

*Figura - Criar pontos finais de recuperação de desastres*

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>Passo 3: Configurar a verificação de saúde e a configuração de failover

Neste passo, você define o DNS TTL para 10 segundos, que é homenageado pela maioria das correções recurivas voltadas para a Internet. Esta configuração significa que nenhum DNS resolver irá cache a informação por mais de 10 segundos. Para as definições do monitor de ponto final, o caminho é definido a corrente em/ou raiz, mas você pode personalizar as definições do ponto final para avaliar um caminho, por exemplo, prod.contoso.com/index. O exemplo abaixo mostra o **https** como o protocolo de sondagem. No entanto, também pode escolher **http** ou **TCP.** A escolha do protocolo depende da aplicação final. O intervalo de sondagem é definido para 10 segundos, o que permite uma sondagem rápida, e a repetição está definida para 3. Como resultado, o Traffic Manager falhará até ao segundo ponto final se três intervalos consecutivos registarem uma falha. A seguinte fórmula define o tempo total para uma falha automatizada: Tempo para falhação = TTL + Repetição * Intervalo de sondagem E neste caso, o valor é de 10 + 3 * 10 = 40 segundos (Máx).
Se a Retry estiver definida para 1 e TTL estiver definida para 10 segundos, então o tempo para falhar 10 + 1 * 10 = 20 segundos. Desaperte a Retry a um valor superior a **1** para eliminar as chances de falhas devido a falsos positivos ou a quaisquer pequenos blips de rede. 


![Configurar o cheque de saúde](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

*Figura - Configurar a configuração de verificação de saúde e failover*

### <a name="how-automatic-failover-works-using-traffic-manager"></a>Como funciona a falha automática usando o Gestor de Tráfego

Durante um desastre, o principal ponto final é sondado e o estado muda para **degradar-se** e o local de recuperação de desastres permanece **On-line**. Por predefinição, o Gestor de Tráfego envia todo o tráfego para o ponto final primário (com a prioridade mais alta). Se o ponto final primário parecer degradado, o Gestor de Tráfego encaminha o tráfego para o segundo ponto final, desde que se mantenha saudável. Uma pessoa tem a opção de configurar mais pontos finais dentro do Traffic Manager que podem servir como pontos finais adicionais de failover, ou, como equilibradores de carga que partilham a carga entre pontos finais.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [o Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md).
- Saiba mais sobre [o Azure DNS](../dns/dns-overview.md).