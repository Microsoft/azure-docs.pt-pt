---
title: Planejando a migração de recursos de IaaS do clássico para o Azure Resource Manager
description: Planejando a migração de recursos de IaaS do clássico para o Azure Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 78492a2c-2694-4023-a7b8-c97d3708dcb7
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 04/01/2017
ms.author: kasing
ms.openlocfilehash: 8dc1ee85b9d17824898de80562ea5bfb251a2c41
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035706"
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Planejando a migração de recursos de IaaS do clássico para o Azure Resource Manager
Embora Azure Resource Manager ofereça muitos recursos incríveis, é essencial planejar sua jornada de migração para garantir que as coisas sejam tranqüilas. Gastar tempo no planejamento garantirá que você não encontre problemas durante a execução de atividades de migração. 

> [!NOTE] 
> As diretrizes a seguir foram bastante contribuídas pela equipe de consultoria do cliente do Azure e pelos arquitetos de soluções de nuvem trabalhando com os clientes na migração de ambientes grandes. Assim, esse documento continuará a ser atualizado à medida que novos padrões de sucesso surgirem, portanto, volte de tempos em tempos para ver se há novas recomendações.

Há quatro fases gerais da jornada de migração:

![Fases de migração](../media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>Planear

### <a name="technical-considerations-and-tradeoffs"></a>Considerações técnicas e compensações

Dependendo do tamanho dos seus requisitos técnicos, das áreas geográficas e das práticas operacionais, talvez você queira considerar:

1. Por que Azure Resource Manager desejado para sua organização?  Quais são os motivos comerciais para uma migração?
2. Quais são os motivos técnicos para Azure Resource Manager?  O que (se houver) serviços adicionais do Azure você gostaria de aproveitar?
3. Qual aplicativo (ou conjuntos de máquinas virtuais) está incluído na migração?
4. Quais cenários têm suporte com a API de migração?  Examine os [recursos e as configurações sem suporte](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations).
5. Suas equipes operacionais agora dão suporte a aplicativos/VMs tanto no clássico quanto no Azure Resource Manager?
6. Como (se houver) Azure Resource Manager alterar os processos de implantação, gerenciamento, monitoramento e relatórios da VM?  Seus scripts de implantação precisam ser atualizados?
7. Qual é o plano de comunicações para alertar os participantes (usuários finais, proprietários de aplicativos e proprietários de infraestrutura)?
8. Dependendo da complexidade do ambiente, deve haver um período de manutenção em que o aplicativo não esteja disponível para os usuários finais e para os proprietários do aplicativo?  Se sim, durante quanto tempo?
9. Qual é o plano de treinamento para garantir que as partes interessadas sejam experientes e proficientes no Azure Resource Manager?
10. O que é o plano de gerenciamento do programa ou de gerenciamento de projeto para a migração?
11. Quais são as linhas do tempo para a migração de Azure Resource Manager e outros mapas de estrada de tecnologia relacionados?  Eles estão alinhados de maneira ideal?

### <a name="patterns-of-success"></a>Padrões de sucesso

Clientes bem-sucedidos têm planos detalhados em que as perguntas anteriores são discutidas, documentadas e governadas.  Verifique se os planos de migração são amplamente comunicados a patrocinadores e participantes.  Equipar-se com conhecimento sobre suas opções de migração; é altamente recomendável ler esse conjunto de documentos de migração abaixo.

* [Visão geral da migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Usar o PowerShell para migrar recursos de IaaS do clássico para o Azure Resource Manager](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Usar a CLI para migrar recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ferramentas da Comunidade para auxiliar na migração de recursos de IaaS do clássico para o Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Examine as perguntas mais frequentes sobre a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="pitfalls-to-avoid"></a>Armadilhas a serem evitadas

- Falha ao planejar.  As etapas de tecnologia dessa migração são comprovadas e o resultado é previsível.
- Suposição de que a API de migração com suporte da plataforma considerará todos os cenários. Leia os [recursos e as configurações sem suporte](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations) para entender quais cenários têm suporte.
- Não planejando uma possível interrupção do aplicativo para usuários finais.  Planeje o buffer suficiente para avisar adequadamente os usuários finais de tempo de aplicativo potencialmente indisponível.


## <a name="lab-test"></a>Teste de laboratório 

**Replicar seu ambiente e fazer uma migração de teste**
  > [!NOTE]
  > A replicação exata do ambiente existente é executada usando uma ferramenta que contribui para a Comunidade, que não é oficialmente suportada pelo Suporte da Microsoft. Portanto, é uma etapa **opcional** , mas é a melhor maneira de descobrir problemas sem tocar em seus ambientes de produção. Se usar uma ferramenta de contribuição da Comunidade não for uma opção, leia sobre a recomendação validar/preparar/anular execução abaixo.
  >
  
  Realizar um teste de laboratório de seu cenário exato (computação, rede e armazenamento) é a melhor maneira de garantir uma migração tranqüila. Isso ajudará a garantir:

- Um laboratório totalmente separado ou um ambiente de não produção existente para teste. Recomendamos um laboratório totalmente separado que pode ser migrado repetidamente e pode ser modificado de forma destrutiva.  Os scripts para coletar/hidratar os metadados das assinaturas reais estão listados abaixo.
- É uma boa ideia criar o laboratório em uma assinatura separada. O motivo é que o laboratório será interrompido repetidamente e ter uma assinatura separada e isolada reduzirá a chance de que algo real seja excluído acidentalmente.

  Isso pode ser feito usando a ferramenta AsmMetadataParser. [Leia mais sobre esta ferramenta aqui](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>Padrões de sucesso

Os problemas a seguir foram descobertos em muitas das migrações maiores. Essa não é uma lista completa e você deve consultar os [recursos e as configurações sem suporte](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations) para obter mais detalhes. Você pode ou não encontrar esses problemas técnicos, mas se você solucioná-los antes de tentar a migração, o garantirá uma experiência mais tranqüila.

- **Faça uma simulação de validação/preparação/anulação** -essa é talvez a etapa mais importante para garantir o sucesso da migração de Azure Resource Manager clássico. A API de migração tem três etapas principais: validar, preparar e confirmar. Validar lerá o estado do seu ambiente clássico e retornará um resultado de todos os problemas. No entanto, como alguns problemas podem existir na pilha de Azure Resource Manager, Validate não detectará tudo. A próxima etapa no processo de migração, preparar ajudará a expor esses problemas. A preparação moverá os metadados do clássico para o Azure Resource Manager, mas não confirmará a movimentação e não removerá nem alterará nada no lado clássico. A simulação envolve a preparação da migração e a anulação (**não confirmação**) da preparação da migração. O objetivo de validar/preparar/anular a execução seca é ver todos os metadados na pilha Azure Resource Manager, examiná-los (*programaticamente ou no portal*) e verificar se tudo está migrado corretamente e trabalhar com problemas técnicos.  Ele também dará uma noção da duração da migração para que você possa planejar o tempo de inatividade de acordo.  Uma validação/preparação/anulação não causa nenhum tempo de inatividade do usuário; Portanto, ele não causa interrupções no uso do aplicativo.
  - Os itens a seguir precisarão ser resolvidos antes do seca ser executado, mas um teste de simulação também eliminará com segurança essas etapas de preparação se elas forem perdidas. Durante a migração para a empresa, descobrimos que a simulação é uma maneira segura e inestimável de garantir a prontidão da migração.
  - Quando a preparação estiver em execução, o plano de controle (operações de gerenciamento do Azure) será bloqueado para toda a rede virtual e, portanto, nenhuma alteração poderá ser feita nos metadados da VM durante a validação/preparação/anulação.  Caso contrário, qualquer função de aplicativo (RD, uso de VM, etc.) não será afetada.  Os usuários das VMs não saberão que a execução seca está sendo executada.

- **Circuitos de rota expressa e VPN**. Atualmente, os gateways de rota expressa com links de autorização não podem ser migrados sem tempo de inatividade. Para obter a solução alternativa, consulte [migrar circuitos de ExpressRoute e redes virtuais associadas do modelo de implantação clássico para o Gerenciador de recursos](../../expressroute/expressroute-migration-classic-resource-manager.md).

- **Extensões de VM** -as extensões de máquina virtual são potencialmente um dos maiores obstáculos para migrar VMs em execução. A correção de extensões de VM pode levar mais de 1-2 dias, portanto, planeje adequadamente.  Um agente do Azure em funcionamento é necessário para reportar o status de extensão da VM de VMs em execução. Se o status retornar como inadequado para uma VM em execução, isso interromperá a migração. O agente em si não precisa estar em ordem de funcionamento para habilitar a migração, mas se existirem extensões na VM, tanto o agente funcional quanto a conectividade de Internet de saída (com DNS) serão necessários para que a migração avance.
  - Se a conectividade com um servidor DNS for perdida durante a migração, todas as extensões de VM, exceto BGInfo v1.\* precisa primeiro ser removido de todas as VMs antes da preparação da migração e subsequentemente adicionada novamente à VM após a migração Azure Resource Manager.  **Isso é apenas para VMs que estão em execução.**  Se as VMs forem interrompidas desalocadas, as extensões de VM não precisarão ser removidas. **Observação:** Muitas extensões como o diagnóstico do Azure e o monitoramento da central de segurança serão reinstalados após a migração, portanto, removê-las não é um problema.
  - Além disso, verifique se os grupos de segurança de rede não estão restringindo o acesso de saída à Internet. Isso pode acontecer com algumas configurações de grupos de segurança de rede. O acesso à Internet de saída (e DNS) é necessário para que as extensões de VM sejam migradas para Azure Resource Manager. 
  - Há duas versões da extensão BGInfo: v1 e v2.  Se a VM tiver sido criada usando o portal do Azure ou o PowerShell, provavelmente a VM terá a extensão v1. Essa extensão não precisa ser removida e será ignorada (não migrada) pela API de migração. No entanto, se a VM clássica foi criada com o novo portal do Azure, ela provavelmente terá a versão v2 baseada em JSON do BGInfo, que pode ser migrada para Azure Resource Manager desde que o agente esteja funcionando e tenha acesso de saída à Internet (e DNS). 
  - **Opção de correção 1**. Se você souber que suas VMs não terão acesso de saída à Internet, um serviço DNS de trabalho e agentes de trabalho do Azure nas VMs, desinstale todas as extensões de VM como parte da migração antes de preparar e reinstale as extensões de VM após a migração. 
  - **Opção de correção 2**. Se as extensões de VM forem muito grandes de um obstáculo, outra opção será desligar/desalocar todas as VMs antes da migração. Migre as VMs desalocadas e reinicie-as no lado do Azure Resource Manager. O benefício aqui é que as extensões de VM serão migradas. A desvantagem é que todos os IPs virtuais voltados para o público serão perdidos (isso pode ser um não-iniciador) e, obviamente, as VMs serão desligadas causando um impacto muito maior nos aplicativos de trabalho.

    > [!NOTE] 
    > Se uma política da central de segurança do Azure estiver configurada em VMs em execução migradas, a política de segurança precisará ser interrompida antes de remover as extensões; caso contrário, a extensão de monitoramento de segurança será reinstalada automaticamente na VM após sua remoção.

- **Conjuntos de disponibilidade** -para que uma vNet (rede virtual) seja migrada para o Azure Resource Manager, a implantação clássica (ou seja, o serviço de nuvem) continha máquinas virtuais deve estar em um conjunto de disponibilidade ou as VMs não devem estar em nenhum conjunto de disponibilidade. Ter mais de um conjunto de disponibilidade no serviço de nuvem não é compatível com Azure Resource Manager e interromperá a migração.  Além disso, não pode haver algumas VMs em um conjunto de disponibilidade e algumas VMs que não estão em um conjunto de disponibilidade. Para resolver isso, será necessário corrigir ou reembaralhar seu serviço de nuvem.  Planeje de forma adequada, pois isso pode ser demorado. 

- **Implantações de função Web/de trabalho** – serviços de nuvem que contêm funções Web e de trabalho não podem migrar para Azure Resource Manager. As funções Web/de trabalho devem primeiro ser removidas da rede virtual antes que a migração possa ser iniciada.  Uma solução típica é apenas mover instâncias de função Web/de trabalho para uma rede virtual clássica separada que também esteja vinculada a um circuito de ExpressRoute ou migrar o código para serviços de aplicativos de PaaS mais recentes (essa discussão está além do escopo deste documento). No primeiro caso de reimplantação, crie uma nova rede virtual clássica, mova/reimplante as funções Web/de trabalho para essa nova rede virtual e, em seguida, exclua as implantações da rede virtual que está sendo movida. Nenhuma alteração de código necessária. O novo recurso de [emparelhamento de rede virtual](../../virtual-network/virtual-network-peering-overview.md) pode ser usado para emparelhar a rede virtual clássica que contém as funções Web/de trabalho e outras redes virtuais na mesma região do Azure, como a rede virtual que está sendo migrada (**após a rede virtual a migração é concluída, pois as redes virtuais emparelhadas não podem ser migradas**), fornecendo, portanto, os mesmos recursos sem perda de desempenho e sem penalidades de latência/largura de banda. Devido à adição de [emparelhamento de rede virtual](../../virtual-network/virtual-network-peering-overview.md), as implantações de função Web/de trabalho agora podem ser facilmente atenuadas e não bloquear a migração para Azure Resource Manager.

- **Cotas de Azure Resource Manager** -as regiões do Azure têm cotas/limites separados para o clássico e o Azure Resource Manager. Embora, em um cenário de migração, um novo hardware não esteja sendo consumido *(estamos trocando VMs existentes do clássico para o Azure Resource Manager)* , Azure Resource Manager cotas ainda precisam estar em vigor com capacidade suficiente antes que a migração possa ser iniciada. Abaixo estão listados os principais limites que vimos causam problemas.  Abra um tíquete de suporte de cota para aumentar os limites. 

    > [!NOTE]
    > Esses limites precisam ser gerados na mesma região que o ambiente atual a ser migrado.
    >

  - Interfaces de Rede
  - Balanceador de Carga
  - IPs públicos
  - IPs públicos estáticos
  - Núcleos
  - Grupos de Segurança de Rede
  - Tabelas de Rota

    Você pode verificar suas cotas de Azure Resource Manager atuais usando os comandos a seguir com a versão mais recente do CLI do Azure.

    **Computação** *(núcleos, conjuntos de disponibilidade)*

    ```bash
    az vm list-usage -l <azure-region> -o jsonc 
    ```

    **Rede** *(redes virtuais, IPS públicos estáticos, IPS públicos, grupos de segurança de rede, interfaces de rede, balanceadores de carga, tabelas de rotas)*
    
    ```bash
    az network list-usages -l <azure-region> -o jsonc
    ```

    **Armazenamento** *(conta de armazenamento)*
    
    ```bash
    az storage account show-usage
    ```

- **Limites de limitação de API Azure Resource Manager** -se você tiver um ambiente grande o suficiente (por exemplo, > VMs 400 em uma VNET), você pode atingir os limites de limitação de API padrão para gravações (atualmente **1200 gravações/hora**) em Azure Resource Manager. Antes de iniciar a migração, você deve gerar um tíquete de suporte para aumentar esse limite para sua assinatura.

- **Status de VM tempo limite de provisionamento** -se alguma VM tiver o status de **provisionamento**expirado, isso precisará ser resolvido antes da migração. A única maneira de fazer isso é com o tempo de inatividade ao desprovisionar/reprovisionar a VM (excluí-la, manter o disco e recriar a VM). 

- **Status da VM RoleStateUnknown** – se a migração for interrompida devido a uma mensagem de erro de **estado de função desconhecida** , inspecione a VM usando o portal e verifique se ela está em execução. Esse erro normalmente desaparecerá por si só (sem necessidade de correção) depois de alguns minutos e, geralmente, é um tipo transitório geralmente visto durante uma máquina virtual de **Iniciar**, **parar**, **reiniciar** as operações. **Prática recomendada:** tente novamente a migração após alguns minutos. 

- **O cluster de malha não existe** -em alguns casos, determinadas VMs não podem ser migradas por vários motivos estranhos. Um desses casos conhecidos é se a VM foi criada recentemente (na última semana ou assim) e ocorreu um cluster do Azure que ainda não está equipado para cargas de trabalho de Azure Resource Manager.  Você receberá um erro informando que o **cluster de malha não existe** e a VM não pode ser migrada. Esperar alguns dias geralmente resolverá esse problema específico, pois o cluster será Azure Resource Manager habilitado em breve. No entanto, uma solução alternativa imediata é `stop-deallocate` a VM, continuar com a migração e iniciar o backup da VM em Azure Resource Manager após a migração.

### <a name="pitfalls-to-avoid"></a>Armadilhas a serem evitadas

- Não use atalhos e omita as migrações de simulação de validação/preparação/anulação.
- A maioria, se não todos, de seus problemas em potencial será surgir durante as etapas de validação/preparação/anulação.

## <a name="migration"></a>Migração

### <a name="technical-considerations-and-tradeoffs"></a>Considerações técnicas e compensações

Agora você está pronto porque trabalhou com os problemas conhecidos com seu ambiente.

Para as migrações reais, talvez você queira considerar:

1. Planeje e agende a rede virtual (menor unidade de migração) com prioridade crescente.  Faça as redes virtuais simples primeiro e o progresso com as redes virtuais mais complicadas.
2. A maioria dos clientes terá ambientes de produção e de não produção.  Agendar produção por último.
3. **(Opcional)** Agende um tempo de inatividade de manutenção com muito buffer caso surjam problemas inesperados.
4. Comunique-se e alinhe-se com suas equipes de suporte caso ocorram problemas.

### <a name="patterns-of-success"></a>Padrões de sucesso

As diretrizes técnicas da seção de teste de laboratório acima devem ser consideradas e atenuadas antes de uma migração real.  Com os testes adequados, a migração é, na verdade, um não evento.  Para ambientes de produção, pode ser útil ter suporte adicional, como um parceiro confiável da Microsoft ou serviços Premier da Microsoft.

### <a name="pitfalls-to-avoid"></a>Armadilhas a serem evitadas

O teste não totalmente pode causar problemas e atraso na migração.  

## <a name="beyond-migration"></a>Além da migração

### <a name="technical-considerations-and-tradeoffs"></a>Considerações técnicas e compensações

Agora que você está em Azure Resource Manager, maximize a plataforma.  Leia a [visão geral de Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) para saber mais sobre os benefícios adicionais.

Coisas a considerar:

- Agrupar a migração com outras atividades.  A maioria dos clientes opta por uma janela de manutenção do aplicativo.  Nesse caso, talvez você queira usar esse tempo de inatividade para habilitar outros recursos de Azure Resource Manager, como criptografia e migração para Managed Disks.
- Reveja os motivos técnicos e comerciais para Azure Resource Manager; Habilite os serviços adicionais disponíveis somente em Azure Resource Manager que se aplicam ao seu ambiente.
- Modernizar seu ambiente com serviços de PaaS.

### <a name="patterns-of-success"></a>Padrões de sucesso

Não se esqueça de quais serviços você agora deseja habilitar no Azure Resource Manager.  Muitos clientes consideram os seguintes atrativos para seus ambientes do Azure:

- [Controle de acesso baseado em função](../../role-based-access-control/overview.md).
- [Azure Resource Manager modelos para implantação mais fácil e mais controlada](../../azure-resource-manager/template-deployment-overview.md).
- [Marcações](../../azure-resource-manager/resource-group-using-tags.md).
- [Controle de atividade](../../azure-resource-manager/resource-group-audit.md)
- [Políticas do Azure](../../governance/policy/overview.md)

### <a name="pitfalls-to-avoid"></a>Armadilhas a serem evitadas

Lembre-se do motivo pelo qual você começou este clássico para Azure Resource Manager a jornada de migração.  Quais foram os motivos comerciais originais? Você obteve o motivo comercial?


## <a name="next-steps"></a>Passos seguintes

* [Visão geral da migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Usar o PowerShell para migrar recursos de IaaS do clássico para o Azure Resource Manager](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Ferramentas da Comunidade para auxiliar na migração de recursos de IaaS do clássico para o Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Examine as perguntas mais frequentes sobre a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
