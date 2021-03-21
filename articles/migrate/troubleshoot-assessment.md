---
title: Avaliação da resolução de problemas e visualização da dependência em Azure Migrate
description: Obtenha ajuda com a avaliação e visualização da dependência em Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 4eeda2e4e418920522f7a65bef68928963c43ad4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100581791"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>Resolver problemas de avaliação/visualização de dependência

Este artigo ajuda-o a resolver problemas com avaliação e visualização de dependência com [Azure Migrate: Avaliação do servidor](migrate-services-overview.md#azure-migrate-server-assessment-tool).


## <a name="assessment-readiness-issues"></a>Questões de prontidão de avaliação

Corrigir problemas de prontidão de avaliação da seguinte forma:

**Emissão** | **Correção**
--- | ---
Tipo de bota não suportada | O Azure não suporta VMs com um tipo de bota EFI. Recomendamos que converta o tipo de porta-malas em BIOS antes de fazer uma migração. <br/><br/>Pode utilizar a migração do servidor Azure Migrate para lidar com a migração de tais VMs. Converterá o tipo de arranque do VM para BIOS durante a migração.
Sistema operativo Windows suportado condicionalmente | O sistema operativo passou a sua data de fim de suporte e precisa de um Acordo de Apoio Personalizado (CSA) para [apoio no Azure](/troubleshoot/azure/virtual-machines/server-software-support). Considere melhorar antes de migrar para Azure. [Reveja]() informações sobre [a preparação de máquinas que executam o Windows Server 2003](prepare-windows-server-2003-migration.md) para migração para Azure.
Sistema operativo Windows não suportado | O Azure suporta apenas [versões selecionadas do Windows OS](/troubleshoot/azure/virtual-machines/server-software-support). Considere atualizar a máquina antes de migrar para Azure.
Sistematicamente endossado Linux OS | O Azure só [endossa versões linux OS selecionadas](../virtual-machines/linux/endorsed-distros.md). Considere atualizar a máquina antes de migrar para Azure. Consulte também [aqui](#linux-vms-are-conditionally-ready-in-an-azure-vm-assessment) para mais detalhes.
Linux OS não dotado | A máquina pode começar em Azure, mas a Azure não fornece suporte ao sistema operativo. Considere atualizar para uma [versão Linux endossada](../virtual-machines/linux/endorsed-distros.md) antes de migrar para Azure.
Sistema operativo desconhecido | O sistema operativo do VM foi especificado como "Outros" no servidor vCenter. Este comportamento impede a Azure Migrate de verificar a prontidão do Azure do VM. Certifique-se de que o sistema operativo é [suportado](./migrate-support-matrix-vmware-migration.md#azure-vm-requirements) pelo Azure antes de migrar a máquina.
Versão de bit não suportada | Os VMs com sistemas operativos de 32 bits podem arrancar em Azure, mas recomendamos que atualize para 64 bits antes de migrar para Azure.
Requer uma subscrição do Microsoft Visual Studio | A máquina está a executar um sistema operativo cliente Windows, que é suportado apenas através de uma subscrição do Visual Studio.
VM não encontrado para o desempenho de armazenamento necessário | O desempenho de armazenamento (operações de entrada/saída por segundo [IOPS] e produção) necessário para a máquina excede o suporte Azure VM. Reduza os requisitos de armazenamento da máquina antes da migração.
VM não encontrado para o desempenho de rede necessário | O desempenho da rede (in/out) necessário para a máquina excede o suporte Azure VM. Reduza os requisitos de ligação em rede para a máquina.
VM não encontrado no local especificado | Use um local de alvo diferente antes da migração.
Um ou mais discos inadequados | Um ou mais discos ligados ao VM não cumprem os requisitos do Azure. A<br/><br/> Azure Migrate: Atualmente, a Avaliação do Servidor não suporta discos Ultra SSD e avalia os discos com base nos limites do disco para discos geridos premium (32 TB).<br/><br/> Para cada disco ligado ao VM, certifique-se de que o tamanho do disco é < 64 TB (suportado por discos Ultra SSD).<br/><br/> Se não for, reduza o tamanho do disco antes de migrar para Azure, ou use vários discos em Azure e [cubra-os juntos](../virtual-machines/premium-storage-performance.md#disk-striping) para obter limites de armazenamento mais elevados. Certifique-se de que o desempenho (IOPS e produção) necessário por cada disco é suportado por [discos de máquinas virtuais geridos](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)pela Azure .
Um ou mais adaptadores de rede inadequados. | Retire os adaptadores de rede não reutilizados da máquina antes da migração.
A contagem de discos excede o limite | Retire os discos não reutilizados da máquina antes da migração.
O tamanho do disco excede o limite | Azure Migrate: Atualmente, a Avaliação do Servidor não suporta discos Ultra SSD e avalia os discos com base nos limites de disco premium (32 TB).<br/><br/> No entanto, o Azure suporta discos com até 64-TB (suportado por discos Ultra SSD). Reduza os discos para menos de 64 TB antes da migração, ou use vários discos em Azure e [enrgem-os juntos](../virtual-machines/premium-storage-performance.md#disk-striping) para obter limites de armazenamento mais elevados.
Disco indisponível no local especificado | Certifique-se de que o disco está no local do alvo antes de migrar.
Disco indisponível para a redundância especificada | O disco deve utilizar o tipo de armazenamento de redundância definido nas definições de avaliação (LRS por padrão).
Não foi possível determinar a adequação do disco por causa de um erro interno | Tente criar uma nova avaliação para o grupo.
VM com núcleos e memória necessários não encontrados | O Azure não conseguiu encontrar um tipo de VM adequado. Reduza a memória e o número de núcleos da máquina no local antes de migrar.
Não foi possível determinar a adequação da VM por causa de um erro interno | Tente criar uma nova avaliação para o grupo.
Não foi possível determinar a adequação de um ou mais discos devido a um erro interno | Tente criar uma nova avaliação para o grupo.
Não foi possível determinar a adequação de um ou mais adaptadores de rede devido a um erro interno | Tente criar uma nova avaliação para o grupo.
Nenhum tamanho VM encontrado para a moeda de oferta Instância Reservada | Máquina marcada Não é adequada porque o tamanho VM não foi encontrado para a combinação selecionada de RI, oferta e moeda. Editar as propriedades de avaliação para escolher as combinações válidas e recalcular a avaliação. 
Protocolo de Internet pronto condicionalmente | Apenas aplicável às avaliações da Azure VMware Solution (AVS). O AVS não suporta o fator endereços de internet IPv6. Contacte a equipa AVS para obter orientação de reparação se a sua máquina for detetada com o IPv6.

## <a name="suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>Ferramenta de migração sugerida na avaliação de AVS baseada em importações marcada como desconhecida

Para as máquinas importadas através de um ficheiro CSV, a ferramenta de migração padrão dentro e a avaliação AVS é desconhecida. No entanto, para máquinas VMware, recomenda-se a utilização da solução VMware Hybrid Cloud Extension (HCX). [Saiba Mais](../azure-vmware/tutorial-deploy-vmware-hcx.md).

## <a name="linux-vms-are-conditionally-ready-in-an-azure-vm-assessment"></a>Os VMs do Linux estão "condicionadomente prontos" numa avaliação do Azure VM

No caso de VMware e VMs hiper-V, a avaliação do servidor marca os VMs do Linux como "condicionadomente prontos" devido a uma lacuna conhecida na Avaliação do Servidor. 

- A lacuna impede-o de detetar a versão menor do Sistema Linux instalado nos VMs no local.
- Por exemplo, para RHEL 6.10, atualmente a Avaliação do Servidor deteta apenas o RHEL 6 como versão OS. Isto porque o vCenter Server ar o anfitrião Hyper-V não fornece a versão kernel para sistemas operativos Linux VM.
-  Como o Azure apoia apenas versões específicas do Linux, os VMs Linux estão atualmente marcados como condicionadomente prontos na Avaliação do Servidor.
- Pode determinar se o Sistema Linux que funciona nas instalações da VM é endossado em Azure, revendo o [suporte do Azure Linux](../virtual-machines/linux/endorsed-distros.md).
-  Depois de verificar a distribuição endossada, pode ignorar este aviso.

Esta lacuna pode ser resolvida permitindo a descoberta de [aplicações](./how-to-discover-applications.md) nos VMware VMs. A Avaliação do Servidor utiliza o sistema operativo detetado na VM através das credenciais de convidado disponibilizadas. Estes dados do sistema operativo identificam as informações adequadas do SISTEMA no caso dos VMs windows e Linux.

## <a name="operating-system-version-not-available"></a>Versão do sistema operativo não disponível

Para servidores físicos, devem estar disponíveis informações de versão menor do sistema operativo. Se não estiver disponível, contacte o Microsoft Support. Para máquinas VMware, a Avaliação do Servidor utiliza as informações do sistema operativo especificadas para o VM no vCenter Server. No entanto, o vCenter Server não fornece a versão menor para sistemas operativos. Para descobrir a versão menor, é necessário configurar a [descoberta da aplicação.](./how-to-discover-applications.md) Para os VMs Hiper-V, a descoberta da versão menor do sistema operativo não é suportada. 

## <a name="azure-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>SKUs Azure maiores do que no local numa avaliação de Azure VM

A avaliação do servidor Azure Migrate poderá recomendar SKUs Azure VM com mais núcleos e memória do que a atribuição atual no local com base no tipo de avaliação:

- A recomendação VM SKU depende das propriedades de avaliação.
- Isto é afetado pelo tipo de avaliação que realiza na Avaliação do Servidor: *Baseada no Desempenho* ou como no *local*.
- Para as avaliações com base no desempenho, a Avaliação do Servidor considera os dados de utilização das VMs no local (utilização da CPU, da memória, do disco e da rede) para determinar o SKU da VM de destino ideal para as VMs no local. Também adiciona um fator de conforto ao determinar uma utilização eficaz.
- Para o dimensionamento no local, os dados de desempenho não são considerados, e o SKU-alvo é recomendado com base na atribuição no local.

Para mostrar como isto pode afetar as recomendações, vejamos um exemplo:

Temos um VM no local com quatro núcleos e oito GB de memória, com 50% de utilização de CPU e 50% de utilização da memória, e um fator de conforto especificado de 1,3.

-  Se a avaliação for **As no local, recomenda-se** um Azure VM SKU com quatro núcleos e 8 GB de memória.
- Se a avaliação for baseada no desempenho, com base na utilização eficaz do CPU e da memória (50% de 4 núcleos * 1,3 = 2,6 núcleos e 50% da memória de 8 GB * 1,3 = memória de 5,3 GB), recomenda-se o VM SKU mais barato de quatro núcleos (contagem de núcleos suportado mais próximo) e oito GB de memória (tamanho de memória suportado mais próximo).
- [Saiba mais](concepts-assessment-calculation.md#types-of-assessments) sobre o dimensionamento de avaliação.

## <a name="why-is-the-recommended-azure-disk-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Porque é que o disco Azure recomendado é maior do que no local numa avaliação de VM Azure?

A avaliação do servidor Azure Migrate pode recomendar um disco maior com base no tipo de avaliação.
- O tamanho do disco na Avaliação do Servidor depende de duas propriedades de avaliação: critérios de dimensionamento e tipo de armazenamento.
- Se os critérios de dimensionamento forem **baseados em desempenho,** e o tipo de armazenamento for definido como **Automático,** o IOPS e os valores de produção do disco são considerados ao identificar o tipo de disco-alvo (Standard HDD, Standard SSD ou Premium). Recomenda-se então um SKU de disco do tipo disco, e a recomendação considera os requisitos de tamanho do disco no local.
- Se os critérios de dimensionamento forem **baseados no desempenho,** e o tipo de armazenamento for **Premium,** recomenda-se um disco premium SKU em Azure com base nos requisitos de produção, produção e tamanho do disco no local. A mesma lógica é utilizada para executar o tamanho do disco quando os critérios de dimensionamento são **as instalações** e o tipo de armazenamento é **Standard HDD,** **Standard SSD,** ou **Premium**.

Como exemplo, se tiver um disco no local com 32 GB de memória, mas o IOPS agregado para o disco é de 800 IOPS, a Avaliação do Servidor recomenda um disco premium (devido aos requisitos de IOPS mais elevados), e recomenda um disco SKU que possa suportar o IOPS e o tamanho necessários. A correspondência mais próxima neste exemplo seria P15 (256 GB, IOPS de 1100). Embora o tamanho exigido pelo disco no local fosse de 32 GB, a Avaliação do Servidor recomenda um disco maior devido à elevada exigência de IOPS do disco no local.

## <a name="why-is-performance-data-missing-for-someall-vms-in-my-assessment-report"></a>Por que motivo os meus dados de desempenho estão em falta em algumas/todas as VMs no meu relatório de avaliação?

Na avaliação “Baseada no desempenho”, a exportação do relatório de avaliação indica “PercentageOfCoresUtilizedMissing” ou “PercentageOfMemoryUtilizedMissing” quando a aplicação Azure Migrate não consegue recolher os dados de desempenho das VMs no local. Verifique:

- Se as VMs estão ligadas durante o período para o qual está a criar a avaliação
- Se apenas os contadores de memória estiverem ausentes e tentar avaliar as VMs do Hyper-V, verifique se tem a memória dinâmica ativada nessas VMs. Há um problema conhecido atualmente que faz com que a aplicação do Azure Migrate não consiga recolher a utilização da memória dessas VMs.
- Se todos os balcões de desempenho estiverem em falta, certifique-se de que os requisitos de acesso portuário para avaliação estão cumpridos. Saiba mais sobre os requisitos de acesso à porta para [avaliação de VMware,](./migrate-support-matrix-vmware.md#port-access-requirements) [Hiper-V](./migrate-support-matrix-hyper-v.md#port-access) e servidor [físico.](./migrate-support-matrix-physical.md#port-access)
Nota – se algum dos contadores de desempenho estiver ausente, o Azure Migrate: Avaliação do Servidor reverterá para os núcleos/memória alocados no local e recomendará um tamanho de VM em conformidade.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>Porque é que a classificação de confiança da minha avaliação é baixa?

A classificação de confiança é calculada para as avaliações “Baseadas no desempenho” com base na percentagem de [pontos de dados disponíveis](./concepts-assessment-calculation.md#ratings), necessários para calcular a avaliação. Veja abaixo os motivos pelos quais uma avaliação pode obter uma classificação de confiança baixa:

- Não analisou o ambiente durante o tempo para a qual está a criar a avaliação. Por exemplo, se estiver a criar uma avaliação com a duração de desempenho definida para uma semana, terá de aguardar, pelo menos, uma semana após iniciar a deteção de todos os pontos de dados serem recolhidos. Se não puder esperar pela duração, altere a duração do desempenho para um período mais curto e “Recalcule” a avaliação.
 
- A Avaliação do Servidor não é capaz de recolher os dados de desempenho de algumas ou de todas as VMs no período de avaliação. Verifique se as VMs foram ligadas durante a avaliação e se são permitidas ligações de saída nas portas 443. Para as VMs Hyper-V, se a memória dinâmica estiver ativada, os contadores de memória estarão em falta, o que levará a uma classificação de baixa confiança. “Recalcule” a avaliação para refletir as últimas alterações na classificação de confiança. 

- Algumas VMs foram criadas após a deteção numa Avaliação do Servidor ter sido iniciada. Por exemplo, se estiver a criar uma avaliação para o histórico de desempenho do último mês, mas poucas VMs tiverem sido criadas no ambiente há apenas uma semana. Neste caso, os dados de desempenho das novas VMs não vão estar disponíveis durante todo este período e a classificação de confiança seria baixa.

[Saiba mais](./concepts-assessment-calculation.md#confidence-ratings-performance-based) sobre a classificação de confiança.

## <a name="is-the-operating-system-license-included-in-an-azure-vm-assessment"></a>A licença do sistema operativo está incluída numa avaliação do Azure VM?

A Azure Migrate Server Assessment considera atualmente o custo da licença do sistema operativo apenas para máquinas Windows. Os custos de licença das máquinas Linux não são considerados atualmente.

## <a name="how-does-performance-based-sizing-work-in-an-azure-vm-assessment"></a>Como funciona o dimensionamento baseado no desempenho numa avaliação de VM Azure?

A Avaliação do Servidor recolhe continuamente os dados de desempenho das máquinas no local e utiliza-os para recomendar o SKU da VM e o SKU do disco no Azure. [Saiba como os](concepts-assessment-calculation.md#calculate-sizing-performance-based) dados baseados no desempenho são recolhidos.

## <a name="why-is-my-assessment-showing-a-warning-that-it-was-created-with-an-invalid-combination-of-reserved-instances-vm-uptime-and-discount-"></a>Porque é que a minha avaliação mostra um aviso de que foi criada com uma combinação inválida de Instâncias Reservadas, Uptime VM e Discount (%)?
Quando seleciona 'Instâncias Reservadas', o 'Desconto (%)» e as propriedades de 'uptime' VM não são aplicáveis. Como a sua avaliação foi criada com uma combinação inválida destas propriedades, os botões de edição e recalculação são desativados. Por favor, crie uma nova avaliação. [Saiba mais](./concepts-assessment-calculation.md#whats-an-assessment).

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>Não vejo dados de desempenho para alguns adaptadores de rede nos meus servidores físicos

Isto pode acontecer se o servidor físico tiver virtualização Hiper-V ativada. Nestes servidores, devido a uma lacuna no produto, a Azure Migrate descobre atualmente tanto os adaptadores de rede física como virtual. A produção da rede é capturada apenas nos adaptadores de rede virtuais descobertos.

## <a name="recommended-azure-vm-sku-for-my-physical-server-is-oversized"></a>Recomendado Azure VM SKU para o meu servidor físico é enorme

Isto pode acontecer se o servidor físico tiver virtualização Hiper-V ativada. Nestes servidores, a Azure Migrate descobre atualmente os adaptadores de rede física e virtual. Daí, o não. dos adaptadores de rede descobertos é maior do que o real. À medida que a Avaliação do Servidor escolhe um VM Azure que pode suportar o número necessário de adaptadores de rede, isto pode potencialmente resultar num VM de grandes dimensões. [Saiba mais](./concepts-assessment-calculation.md#calculating-sizing) sobre o impacto do não. de adaptadores de rede no dimensionamento. Esta é uma lacuna de produtos que será resolvida daqui para a frente.

## <a name="readiness-category-not-ready-for-my-physical-server"></a>Categoria de prontidão "Não está pronto" para o meu servidor físico

A categoria de prontidão pode ser incorretamente marcada como "Não Pronto" no caso de um servidor físico que tenha a virtualização Hyper-V ativada. Nestes servidores, devido a uma lacuna no produto, a Azure Migrate descobre atualmente tanto os adaptadores físicos como os adaptadores virtuais. Daí, o não. dos adaptadores de rede descobertos é maior do que o real. Tanto nas instalações como nas avaliações baseadas no desempenho, a Avaliação do Servidor escolhe um VM Azure que pode suportar o número necessário de adaptadores de rede. Se se descobrir que o número de adaptadores de rede é superior a 32, o máximo não. dos NICs suportados em VMs Azure, a máquina será marcada "Não está pronta".  [Saiba mais](./concepts-assessment-calculation.md#calculating-sizing) sobre o impacto do não. de NICs no tamanho.


## <a name="number-of-discovered-nics-higher-than-actual-for-physical-servers"></a>Número de NICs descobertos superior ao real para servidores físicos

Isto pode acontecer se o servidor físico tiver virtualização Hiper-V ativada. Nestes servidores, a Azure Migrate descobre atualmente tanto os adaptadores físicos como os adaptadores virtuais. Daí, o não. dos NICs descobertos é maior do que o real.

## <a name="dependency-visualization-in-azure-government"></a>Visualização da dependência no Governo de Azure

A análise da dependência baseada em agentes não é apoiada no Governo de Azure. Por favor, use análise de dependência sem agente.


## <a name="dependencies-dont-show-after-agent-install"></a>Dependências não mostram após a instalação do agente

Depois de ter instalado os agentes de visualização de dependência em VMs no local, a Azure Migrate normalmente demora 15 a 30 minutos a exibir as dependências do portal. Se esperou mais de 30 minutos, certifique-se de que o Agente de Monitorização da Microsoft (MMA) pode ligar-se ao espaço de trabalho do Log Analytics.

Para VMs windows:
1. No Painel de Controlo, inicie o MMA.
2. Nas propriedades do **Agente de Monitorização** da Microsoft  >  **Azure Log Analytics (OMS),** certifique-se de que o **Estado** do espaço de trabalho é verde.
3. Se o estado não for verde, tente remover o espaço de trabalho e adicioná-lo novamente ao MMA.

    ![Estatuto de MMA](./media/troubleshoot-assessment/mma-properties.png)

Para os VMs Linux, certifique-se de que os comandos de instalação para o MMA e o agente de dependência foram bem sucedidos. Consulte [aqui](../azure-monitor/vm/service-map.md#post-installation-issues)mais orientações de resolução de problemas.

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

- **Agente MMS**: Reveja os sistemas operativos [Windows](../azure-monitor/agents/agents-overview.md#supported-operating-systems)e [Linux](../azure-monitor/agents/agents-overview.md#supported-operating-systems) suportados.
- **Agente de dependência:** os sistemas operativos [Windows e Linux](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) suportados.

## <a name="visualize-dependencies-for--hour"></a>Visualizar dependências para > hora

Com uma análise de dependência sem agente, pode visualizar dependências ou exportá-las num mapa por um período máximo de 30 dias.

Com a análise da dependência baseada em agentes, embora a Azure Migrate permita voltar a uma data específica no último mês, a duração máxima para a qual pode visualizar as dependências é de uma hora. Por exemplo, pode utilizar a funcionalidade de duração do tempo no mapa de dependência para visualizar as dependências de ontem, mas pode vê-las apenas por um período de uma hora. No entanto, pode utilizar registos do Azure Monitor para [consultar os dados de dependência](./how-to-create-group-machine-dependencies.md) durante uma duração mais longa.

## <a name="visualized-dependencies-for--10-machines"></a>Dependências visualizadas para > 10 máquinas

Na Avaliação do Servidor Azure Migrate, com análise de dependência baseada em agente, pode [visualizar dependências para grupos](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) com até 10 VMs. Para grupos maiores, recomendamos que divida os VMs em grupos mais pequenos para visualizar as dependências.


## <a name="machines-show-install-agent"></a>Máquinas mostram "Agente de instalação"

Após a migração de máquinas com visualização de dependência ativada para a Azure, as máquinas podem mostrar a ação "Instalar agente" em vez de "Ver dependências" devido ao seguinte comportamento:

- Após a migração para Azure, as máquinas no local são desligadas e vMs equivalentes são girados em Azure. Estas máquinas adquirem um endereço MAC diferente.
- As máquinas também podem ter um endereço IP diferente, com base no facto de ter mantido ou não o endereço IP no local.
- Se tanto os endereços MAC como IP forem diferentes dos locais, a Azure Migrate não associa as máquinas no local a quaisquer dados de dependência do Mapa de Serviço. Neste caso, mostrará a opção de instalar o agente em vez de visualizar dependências.
- Após uma migração de teste para Azure, as máquinas no local permanecem ligadas como esperado. Máquinas equivalentes giradas no Azure adquirem diferentes endereços MAC e podem adquirir diferentes endereços IP. A menos que bloqueie o tráfego de registo do Azure Monitor a partir destas máquinas, a Azure Migrate não associará as máquinas no local a quaisquer dados de dependência do Mapa de Serviço, e assim mostrará a opção de instalar agentes, em vez de ver dependências.

## <a name="dependencies-export-csv-shows-unknown-process"></a>Dependências exportam CSV mostra "processo desconhecido"
Na análise da dependência sem agentes, os nomes do processo são capturados com o melhor esforço. Em certos cenários, embora os nomes dos servidores de origem e destino e a porta de destino sejam capturados, não é possível determinar os nomes do processo em ambas as extremidades da dependência. Nestes casos, o processo é marcado como "processo desconhecido".

## <a name="my-log-analytics-workspace-is-not-listed-when-trying-to-configure-the-workspace-in-server-assessment"></a>O meu espaço de trabalho Log Analytics não está listado quando se tenta configurar o espaço de trabalho na Avaliação do Servidor
Atualmente, o Azure Migrate suporta a criação de áreas de trabalho do OMS nas regiões E.U.A. Leste, Ásia Sudeste e Europa Ocidental. Se o espaço de trabalho for criado fora de Azure Migrate em qualquer outra região, atualmente não pode ser associado a um projeto Azure Migrate.


## <a name="capture-network-traffic"></a>Captação de tráfego de rede

Recolher registos de tráfego de rede da seguinte forma:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Prima F12 para iniciar ferramentas de desenvolvimento. Se necessário, limpe as **entradas Desmarcadas na definição de navegação.**
3. Selecione o separador **'Rede'** e comece a capturar o tráfego da rede:
   - No Chrome, selecione **Registo de Preservação**. A gravação deve começar automaticamente. Um círculo vermelho indica que o tráfego está a ser capturado. Se o círculo vermelho não aparecer, selecione o círculo preto para começar.
   - No Microsoft Edge e no Internet Explorer, a gravação deve começar automaticamente. Se não o fizer, selecione o botão de reprodução verde.
4. Tente reproduzir o erro.
5. Depois de ter encontrado o erro durante a gravação, pare de gravar e guarde uma cópia da atividade gravada:
   - No Chrome, clique à direita e **selecione Guardar como HAR com conteúdo**. Esta ação comprime e exporta os registos como ficheiro .har.
   - No Microsoft Edge ou no Internet Explorer, selecione a opção **de tráfego capturado Export.** Esta ação comprime e exporta o tronco.
6. Selecione o **separador Consola** para verificar se há avisos ou erros. Para guardar o registo da consola:
   - No Chrome, clique com o botão direito em qualquer lugar do registo da consola. **Selecione Guardar como**, para exportar e fechar o registo.
   - No Microsoft Edge ou no Internet Explorer, clique com o botão direito dos erros e selecione **Copy all**.
7. Feche as ferramentas de desenvolvimento.


## <a name="where-is-the-operating-system-data-in-my-assessment-discovered-from"></a>De onde são descobertos os dados do sistema operativo na minha avaliação?

- Para VMware VMs, por padrão, são os dados do sistema operativo fornecidos pelo vCenter. 
   - Para VMware linux VMs, se a descoberta da aplicação estiver ativada, os detalhes do SO são recolhidos do VM convidado. Para verificar quais os detalhes do SO na avaliação, vá à visualização dos servidores Descobertos e ao rato sobre o valor na coluna "Sistema Operativo". No texto que aparece, poderá ver se os dados de SO que vê são recolhidos do servidor vCenter ou do VM do hóspede utilizando as credenciais VM. 
   - Para os VMs do Windows, os detalhes do sistema operativo são sempre recolhidos a partir do servidor vCenter.
- Para os VMs Hiper-V, os dados do sistema operativo são recolhidos a partir do anfitrião Hyper-V
- Para servidores físicos, é recolhido do servidor.

## <a name="next-steps"></a>Passos seguintes

[Criar](how-to-create-assessment.md) ou [personalizar](how-to-modify-assessment.md) uma avaliação.