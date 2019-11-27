---
title: Recuperação de desastre usando o DNS do Azure e o Gerenciador de tráfego | Microsoft Docs
description: Visão geral das soluções de recuperação de desastre usando o DNS do Azure e o Gerenciador de tráfego.
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
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483539"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Recuperação após desastre com o DNS do Azure e o Gestor de Tráfego

A recuperação de desastres se concentra em recuperar-se de uma perda grave de funcionalidade do aplicativo. Para escolher uma solução de recuperação de desastres, os proprietários de negócios e de tecnologia devem primeiro determinar o nível de funcionalidade que é necessário durante um desastre, como-indisponível, parcialmente disponível por meio de funcionalidade reduzida ou disponibilidade atrasada, ou totalmente disponível.
A maioria dos clientes empresariais está escolhendo uma arquitetura de várias regiões para resiliência em relação a um failover no nível de aplicativo ou infraestrutura. Os clientes podem escolher várias abordagens na Quest para alcançar o failover e a alta disponibilidade por meio da arquitetura redundante. Aqui estão algumas das abordagens populares:

- **Ativo-passivo com espera passiva**: nessa solução de failover, as VMs e outros dispositivos em execução na região em espera não estarão ativos até que haja uma necessidade de failover. No entanto, o ambiente de produção é replicado na forma de backups, imagens de VM ou modelos do Resource Manager para uma região diferente. Esse mecanismo de failover é econômico, mas leva mais tempo para realizar um failover completo.
 
    ![Ativo/passivo com espera fria](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    *Figura-ativo/passivo com configuração de recuperação de desastres em espera frio*

- **Ativo/passivo com luz piloto**: nessa solução de failover, o ambiente em espera é configurado com uma configuração mínima. A instalação tem apenas os serviços necessários em execução para dar suporte apenas a um conjunto mínimo e crítico de aplicativos. Em sua forma nativa, esse cenário só pode executar a funcionalidade mínima, mas pode escalar verticalmente e gerar serviços adicionais para tirar o máximo da carga de produção se ocorrer um failover.
    
    ![Ativo/passivo com luz piloto](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *Figura: ativo/passivo com a configuração de recuperação de desastre do piloto Light*

- **Ativo/passivo com espera passiva**: nessa solução de failover, a região em espera é pré-configurada e está pronta para assumir a carga base, o dimensionamento automático está ativado e todas as instâncias estão em execução. Essa solução não é dimensionada para fazer a carga de produção completa, mas é funcional e todos os serviços estão em execução. Essa solução é uma versão ampliada da abordagem de luz piloto.
    
    ![Ativo/passivo com espera passiva](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    *Figura: ativo/passivo com configuração de recuperação de desastre em espera passiva*
    
Para saber mais sobre failover e alta disponibilidade, consulte [recuperação de desastre para aplicativos do Azure](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).


## <a name="planning-your-disaster-recovery-architecture"></a>Planejando sua arquitetura de recuperação de desastres

Há dois aspectos técnicos em relação à configuração de sua arquitetura de recuperação de desastres:
-  Usar um mecanismo de implantação para replicar instâncias, dados e configurações entre os ambientes primário e em espera. Esse tipo de recuperação de desastre pode ser feito nativamente por meio da recuperação de site do Azure por meio de dispositivos/serviços de parceiros da Microsoft Azure, como Veritas ou NetApp. 
- Desenvolver uma solução para desviar o tráfego de rede/Web do site primário para o site em espera. Esse tipo de recuperação de desastre pode ser obtido por meio do DNS do Azure, do Azure Traffic Manager (DNS) ou de balanceadores de carga global de terceiros.

Este artigo é limitado a abordagens por meio de redirecionamento de tráfego da Web e de rede. Para obter instruções sobre como configurar Azure Site Recovery, consulte a [documentação do Azure site Recovery](https://docs.microsoft.com/azure/site-recovery/).
O DNS é um dos mecanismos mais eficientes para desviar o tráfego de rede porque o DNS geralmente é global e externo à data center e é isolado de quaisquer falhas de nível de AZ (zona regional ou de disponibilidade). É possível usar um mecanismo de failover baseado em DNS e, no Azure, dois serviços DNS podem ser iguais de alguma maneira – DNS do Azure (DNS autoritativo) e Gerenciador de tráfego do Azure (roteamento de tráfego inteligente baseado em DNS). 

É importante entender alguns conceitos no DNS que são amplamente usados para discutir as soluções fornecidas neste artigo:
- **Registro A de DNS** – registros a são ponteiros que apontam um domínio para um endereço IPv4. 
- **Nome CNAME ou canônico** -esse tipo de registro é usado para apontar para outro registro DNS. O CNAME não responde com um endereço IP, mas sim o ponteiro para o registro que contém o endereço IP. 
- **Roteamento ponderado** – um pode optar por associar um peso aos pontos de extremidade de serviço e, em seguida, distribuir o tráfego com base nos pesos atribuídos. Esse método de roteamento é um dos quatro mecanismos de roteamento de tráfego disponíveis no Gerenciador de tráfego. Para obter mais informações, consulte [método de roteamento ponderado](../traffic-manager/traffic-manager-routing-methods.md#weighted).
- **Roteamento prioritário** – o roteamento de prioridade baseia-se nas verificações de integridade dos pontos de extremidade. Por padrão, o Gerenciador de tráfego do Azure envia todo o tráfego para o ponto de extremidade de prioridade mais alta e, após uma falha ou desastre, o Gerenciador de tráfego roteia o tráfego para o ponto de extremidade secundário. Para obter mais informações, consulte [método de roteamento de prioridade](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method).

## <a name="manual-failover-using-azure-dns"></a>Failover manual usando o DNS do Azure
A solução de failover manual do DNS do Azure para recuperação de desastres usa o mecanismo DNS padrão para fazer failover para o site de backup. A opção manual por meio do DNS do Azure funciona melhor quando usada em conjunto com o modo de espera frio ou a abordagem de luz piloto. 

![Failover manual usando o DNS do Azure](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

*Figura-failover manual usando o DNS do Azure*

As suposições feitas para a solução são:
- Os pontos de extremidade primários e secundários têm IPs estáticos que não são alterados com frequência. Digamos que, para o site primário, o IP seja 100.168.124.44 e o IP para o site secundário seja 100.168.124.43.
- Uma zona DNS do Azure existe para o site primário e secundário. Digamos que, para o site primário, o ponto de extremidade seja prod.contoso.com e o site de backup seja dr.contoso.com. Um registro DNS para o aplicativo principal conhecido como www\.contoso.com também existe.   
- A TTL está abaixo ou abaixo do SLA de RTO definido na organização. Por exemplo, se uma empresa define o RTO da resposta de desastre do aplicativo como sendo 60 minutos, a TTL deve ser inferior a 60 minutos, preferencialmente o mais baixo possível. 
  Você pode configurar o DNS do Azure para failover manual da seguinte maneira:
- Criar uma zona DNS
- Criar registros de zona DNS
- Atualizar registro CNAME

### <a name="step-1-create-a-dns"></a>Etapa 1: criar um DNS
Crie uma zona DNS (por exemplo, www\.contoso.com), conforme mostrado abaixo:

![Criar uma zona DNS no Azure](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

*Figura-criar uma zona DNS no Azure*

### <a name="step-2-create-dns-zone-records"></a>Etapa 2: criar registros de zona DNS

Nessa zona, crie três registros (por exemplo, www\.contoso.com, prod.contoso.com e dr.consoto.com), conforme mostrado abaixo.

![Criar registros de zona DNS](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

*Figura-criar registros de zona DNS no Azure*

Nesse cenário, site, www\.contoso.com tem um TTL de 30 minutos, que está bem abaixo do RTO declarado e está apontando para o site de produção prod.contoso.com. Essa configuração é durante as operações comerciais normais. A TTL de prod.contoso.com e dr.contoso.com foi definida como 300 segundos ou 5 minutos. Você pode usar um serviço de monitoramento do Azure, como o Azure Monitor ou o Azure App insights, ou qualquer solução de monitoramento de parceiro, como o dynaTrace, você pode até mesmo usar soluções de crescimento doméstico que podem monitorar ou detectar falhas no nível do aplicativo ou da infraestrutura virtual.

### <a name="step-3-update-the-cname-record"></a>Etapa 3: atualizar o registro CNAME

Quando a falha for detectada, altere o valor do registro para apontar para dr.contoso.com, conforme mostrado abaixo:
       
![Atualizar registro CNAME](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

*Figura-atualizar o registro CNAME no Azure*

Dentro de 30 minutos, durante o qual a maioria dos resolvedores atualizará o arquivo de zona armazenado em cache, qualquer consulta ao www\.contoso.com será redirecionada para dr.contoso.com.
Você também pode executar o seguinte comando CLI do Azure para alterar o valor de CNAME:
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
Esta etapa pode ser executada manualmente ou por meio de automação. Isso pode ser feito manualmente por meio do console do ou pelo CLI do Azure. O SDK e a API do Azure podem ser usados para automatizar a atualização de CNAME para que nenhuma intervenção manual seja necessária. A automação pode ser criada por meio do Azure Functions ou de um aplicativo de monitoramento de terceiros ou até mesmo do local.

### <a name="how-manual-failover-works-using-azure-dns"></a>Como funciona o failover manual usando o DNS do Azure
Como o servidor DNS está fora do failover ou da zona de desastre, ele é isolado contra qualquer tempo de inatividade. Isso permite que o usuário projete um cenário de failover simples que seja econômico e funcione o tempo todo, supondo que o operador tenha conectividade de rede durante o desastre e possa fazer a inversão. Se a solução tiver o script, será necessário garantir que o servidor ou serviço que executa o script seja isolado em relação ao problema que afeta o ambiente de produção. Além disso, tenha em mente o TTL baixo que foi definido em relação à zona para que nenhum resolvedor em todo o mundo Mantenha o ponto de extremidade em cache por longos e os clientes possam acessar o site dentro do RTO. Para uma luz fria e piloto frio, uma vez que algumas atividades administrativas e de aquecimento podem ser necessárias – uma delas também deve fornecer tempo suficiente antes de fazer a inversão.

## <a name="automatic-failover-using-azure-traffic-manager"></a>Failover automático usando o Gerenciador de tráfego do Azure
Quando você tem arquiteturas complexas e vários conjuntos de recursos capazes de executar a mesma função, você pode configurar o Gerenciador de tráfego do Azure (com base no DNS) para verificar a integridade de seus recursos e rotear o tráfego do recurso não íntegro para o estado íntegro Kit. No exemplo a seguir, a região primária e a região secundária têm uma implantação completa. Essa implantação inclui os serviços de nuvem e um banco de dados sincronizado. 

![Failover automático usando o Gerenciador de tráfego do Azure](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

*Figura – failover automático usando o Gerenciador de tráfego do Azure*

No entanto, somente a região primária está manipulando ativamente as solicitações de rede dos usuários. A região secundária se torna ativa somente quando a região primária passa por uma interrupção do serviço. Nesse caso, todas as novas solicitações de rede são roteadas para a região secundária. Como o backup do banco de dados é quase instantâneo, ambos os balanceadores de carga têm IPs que podem ser verificados de integridade e as instâncias estão sempre em execução, essa topologia fornece uma opção para entrar em um baixo RTO e failover sem nenhuma intervenção manual. A região de failover secundário deve estar pronta para ficar ao vivo imediatamente após a falha da região primária.
Esse cenário é ideal para o uso do Gerenciador de tráfego do Azure que tem investigações embutidas para vários tipos de verificações de integridade, incluindo http/https e TCP. O Gerenciador de tráfego do Azure também tem um mecanismo de regra que pode ser configurado para failover quando ocorre uma falha, conforme descrito abaixo. Vamos considerar a seguinte solução usando o Gerenciador de tráfego:
- O cliente tem a região #1 ponto de extremidade conhecido como prod.contoso.com com um IP estático como 100.168.124.44 e uma região #2 ponto de extremidade conhecido como dr.contoso.com com um IP estático como 100.168.124.43. 
-   Cada um desses ambientes é direcionado por meio de uma propriedade voltada para o público, como um balanceador de carga. O balanceador de carga pode ser configurado para ter um ponto de extremidade baseado em DNS ou um FQDN (nome de domínio totalmente qualificado), como mostrado acima.
-   Todas as instâncias na região 2 estão em replicação quase em tempo real com a região 1. Além disso, as imagens do computador estão atualizadas e todos os dados de software/configuração são corrigidos e estão em linha com a região 1.  
-   O dimensionamento automático é pré-configurado antecipadamente. 

As etapas executadas para configurar o failover com o Gerenciador de tráfego do Azure são as seguintes:
1. Criar um novo perfil do Gerenciador de tráfego do Azure
2. Criar pontos de extremidade no perfil do Gerenciador de tráfego
3. Configurar verificação de integridade e configuração de failover

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>Etapa 1: criar um novo perfil do Gerenciador de tráfego do Azure
Crie um novo perfil do Gerenciador de tráfego do Azure com o nome contoso123 e selecione o método de roteamento como prioridade. Se você tiver um grupo de recursos pré-existente com o qual deseja associar, poderá selecionar um grupo de recursos existente, caso contrário, criará um novo grupo de recursos.

![Criar perfil do Gerenciador de tráfego](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)

*Figura-criar um perfil do Gerenciador de tráfego*

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>Etapa 2: criar pontos de extremidade no perfil do Gerenciador de tráfego

Nesta etapa, você cria pontos de extremidade que apontam para os sites de recuperação de desastres e de produção. Aqui, escolha o **tipo** como um ponto de extremidade externo, mas se o recurso estiver hospedado no Azure, você também poderá escolher o **ponto de extremidade do Azure** . Se você escolher **ponto de extremidade do Azure**, selecione um **recurso de destino** que seja um **serviço de aplicativo** ou um **IP público** que é alocado pelo Azure. A prioridade é definida como **1** , pois é o serviço primário para a região 1.
Da mesma forma, crie também o ponto de extremidade de recuperação de desastres no Gerenciador de tráfego.

![Criar pontos de extremidade de recuperação de desastre](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

*Figura-criar pontos de extremidade de recuperação de desastre*

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>Etapa 3: configurar a verificação de integridade e a configuração de failover

Nesta etapa, você define o TTL do DNS como 10 segundos, que é respeitado pela maioria dos resolvedores recursivos voltados para a Internet. Essa configuração significa que nenhum resolvedor de DNS armazenará em cache as informações por mais de 10 segundos. Para as configurações do monitor do ponto de extremidade, o caminho é definido como atual/ou raiz, mas você pode personalizar as configurações do ponto de extremidade para avaliar um caminho, por exemplo, prod.contoso.com/index. O exemplo a seguir mostra o **https** como o protocolo de investigação. No entanto, você também pode escolher **http** ou **TCP** . A escolha do protocolo depende do aplicativo final. O intervalo de investigação é definido como 10 segundos, o que permite uma investigação rápida e a repetição é definida como 3. Como resultado, o Gerenciador de tráfego fará failover para o segundo ponto de extremidade se três intervalos consecutivos registrarem uma falha. A fórmula a seguir define o tempo total para um failover automatizado: tempo para failover = TTL + repetição * investigação do intervalo e, nesse caso, o valor é 10 + 3 * 10 = 40 segundos (máx.).
Se a repetição estiver definida como 1 e TTL for definido como 10 segundos, a hora para o failover 10 + 1 * 10 = 20 segundos. Defina a nova tentativa como um valor maior que **1** para eliminar as chances de failovers devido a falsos positivos ou a qualquer blips de rede secundária. 


![Configurar a verificação de integridade](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

*Figura-configurar verificação de integridade e configuração de failover*

### <a name="how-automatic-failover-works-using-traffic-manager"></a>Como funciona o failover automático usando o Gerenciador de tráfego

Durante um desastre, o ponto de extremidade primário é investigado e o status muda para **degradado** e o site de recuperação de desastre permanece **online**. Por padrão, o Gerenciador de tráfego envia todo o tráfego para o ponto de extremidade primário (prioridade mais alta). Se o ponto de extremidade primário aparecer degradado, o Gerenciador de tráfego roteia o tráfego para o segundo ponto de extremidade desde que ele permaneça íntegro. Uma delas tem a opção de configurar mais pontos de extremidade no Gerenciador de tráfego que podem servir como pontos de extremidade de failover adicionais ou, como balanceadores de carga que compartilham a carga entre os pontos de extremidade.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre o [Gerenciador de tráfego do Azure](../traffic-manager/traffic-manager-overview.md).
- Saiba mais sobre o [DNS do Azure](../dns/dns-overview.md).









