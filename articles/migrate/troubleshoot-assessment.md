---
title: Avaliação de problemas e visualização da dependência em Azure Migrate
description: Obtenha ajuda na avaliação de problemas e visualização da dependência em Azure Migrate.
ms.service: azure-migrate
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/02/2020
ms.openlocfilehash: 205b52201edb849abab02809b58ff9dc77a32a29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127667"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>Resolver problemas de avaliação/visualização de dependência

Este artigo ajuda-o a resolver problemas com a avaliação e visualização da dependência com [a Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool).


## <a name="assessment-readiness-issues"></a>Questões de prontidão de avaliação

Corrigir as questões de prontidão da avaliação da seguinte forma:

**Edição** | **Correção**
--- | ---
Tipo de bota não suportada | O Azure não suporta VMs com um tipo de boot EFI. Recomendamos que converta o tipo de boot em BIOS antes de fazer uma migração. <br/><br/>Pode utilizar a Migração do Servidor Migratório Azure para lidar com a migração de tais VMs. Converterá o tipo de arranque do VM para BIOS durante a migração.
Sistema operativo Windows suportado condicionalmente | O sistema operativo aprovou a data de fim de suporte e precisa de um Acordo de Apoio Personalizado (CSA) para [apoio no Azure](https://aka.ms/WSosstatement). Considere melhorar antes de migrar para Azure.
Sistema operativo Windows não suportado | O Azure suporta apenas [versões selecionadas do Windows OS.](https://aka.ms/WSosstatement) Considere melhorar a máquina antes de migrar para Azure.
Apoiado condicionalmente linux OS | O Azure endossa apenas [versões Linux OS selecionadas.](../virtual-machines/linux/endorsed-distros.md) Considere melhorar a máquina antes de migrar para Azure.
Linux Os não endossado | A máquina pode começar em Azure, mas o Azure não fornece suporte ao sistema operativo. Considere fazer upgrade para uma [versão linux endossada](../virtual-machines/linux/endorsed-distros.md) antes de migrar para o Azure.
Sistema operativo desconhecido | O sistema operativo do VM foi especificado como "Outro" no VCenter Server. Este comportamento impede o Azure Migrate de verificar a prontidão azure do VM. Certifique-se de que o sistema operativo é [suportado](https://aka.ms/azureoslist) pelo Azure antes de migrar a máquina.
Versão bit não suportada | VMs com um sistema operativo de 32 bits podem arrancar em Azure, mas recomendamos que faça o upgrade para 64 bits antes de migrar para O Azure.
Requer uma subscrição do Microsoft Visual Studio | A máquina está a executar um sistema operativo windows cliente, que é suportado apenas através de uma subscrição do Estúdio Visual.
VM não encontrado para o desempenho de armazenamento necessário | O desempenho de armazenamento (operações de entrada/saída por segundo [IOPS] e entrada) necessários para a máquina excede o suporte Azure VM. Reduza os requisitos de armazenamento para a máquina antes da migração.
VM não encontrado para o desempenho de rede necessário | O desempenho da rede (dentro/fora) necessário para a máquina excede o suporte Azure VM. Reduza os requisitos de rede para a máquina.
VM não encontrado no local especificado | Use um local de destino diferente antes da migração.
Um ou mais discos inadequados | Um ou mais discos ligados ao VM não satisfazem os requisitos do Azure. A<br/><br/> Azure Migrate: Atualmente não suporta discos Ultra SSD e avalia os discos com base nos limites do disco para discos geridos premium (32 TB).<br/><br/> Para cada disco ligado ao VM, certifique-se de que o tamanho do disco é < 64 TB (suportado por discos Ultra SSD).<br/><br/> Se não for, reduza o tamanho do disco antes de migrar para Azure, ou use vários discos em Azure e [junte-os](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) para obter limites de armazenamento mais elevados. Certifique-se de que o desempenho (IOPS e a entrada) necessário por cada disco é suportado por [discos de máquinas virtuais geridos](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits)pelo Azure .
Um ou mais adaptadores de rede inadequados. | Retire os adaptadores de rede não utilizados da máquina antes da migração.
Contagem de discos excede limite | Retire os discos não utilizados da máquina antes da migração.
O tamanho do disco excede o limite | Azure Migrate: Atualmente não suporta discos Ultra SSD e avalia os discos com base em limites de disco premium (32 TB).<br/><br/> No entanto, o Azure suporta discos com até 64 TB de tamanho (suportados por discos Ultra SSD). Encolher discos para menos de 64 TB antes da migração, ou usar vários discos em Azure e [listra-los juntos](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) para obter limites de armazenamento mais elevados.
Disco indisponível no local especificado | Certifique-se de que o disco está no local do alvo antes de migrar.
Disco indisponível para a redundância especificada | O disco deve utilizar o tipo de armazenamento de despedimento definido nas definições de avaliação (LRS por padrão).
Não conseguiu determinar a adequação do disco devido a um erro interno | Tente criar uma nova avaliação para o grupo.
VM com núcleos e memória exigidos não encontrados | O Azure não encontrou um tipo de VM adequado. Reduza a memória e o número de núcleos da máquina no local antes de migrar.
Não conseguiu determinar a adequação da VM devido a um erro interno | Tente criar uma nova avaliação para o grupo.
Não poderia determinar a adequação de um ou mais discos devido a um erro interno | Tente criar uma nova avaliação para o grupo.
Não poderia determinar a adequação de um ou mais adaptadores de rede devido a um erro interno | Tente criar uma nova avaliação para o grupo.

## <a name="linux-vms-are-conditionally-ready"></a>VMs Linux estão "condicionados prontos"

A avaliação do servidor marca os VMs Linux como "condicionados prontos" devido a uma lacuna conhecida na Avaliação do Servidor.

- A lacuna impede-a de detetar a versão menor do Sistema Operativo Linux instalada nos VMs no local.
- Por exemplo, para rHEL 6.10, atualmente a Avaliação do Servidor deteta apenas RHEL 6 como a versão OS.
-  Como o Azure apoia apenas versões específicas do Linux, os VMs Linux estão atualmente marcados como condicionados na Avaliação do Servidor.
- Pode determinar se o Sistema Operativo Linux que funciona no VM no local é endossado em Azure através da revisão do [suporte azure Linux](https://aka.ms/migrate/selfhost/azureendorseddistros).
-  Depois de verificar a distribuição endossada, pode ignorar este aviso.

## <a name="azure-skus-bigger-than-on-premises"></a>Uscósios azure maiores do que no local

A Avaliação do Servidor Migratório Azure pode recomendar a Azure VM SKUs com mais núcleos e memória do que a alocação atual no local com base no tipo de avaliação:


- A recomendação VM SKU depende das propriedades de avaliação.
- Isto é afetado pelo tipo de avaliação que executa na Avaliação do Servidor: Baseado no *desempenho,* ou *As no local*.
- Para avaliações baseadas no desempenho, a Avaliação do Servidor considera os dados de utilização dos VMs no local (CPU, memória, disco e utilização da rede) para determinar o vM SKU alvo certo para os seus VMs no local. Também adiciona um fator de conforto ao determinar uma utilização eficaz.
- Para o dimensionamento no local, os dados de desempenho não são considerados, e o SKU alvo é recomendado com base na atribuição de instalações.

Para mostrar como isto pode afetar as recomendações, vamos dar um exemplo:

Temos um VM no local com quatro núcleos e oito GB de memória, com 50% de utilização de CPU e 50% de utilização da memória, e um fator de conforto especificado de 1,3.

-  Se a avaliação for **As on-premis , recomenda-se**um Azure VM SKU com quatro núcleos e 8 GB de memória.
- Se a avaliação for baseada no desempenho, com base na utilização eficaz do CPU e da memória (50% de 4 núcleos * 1,3 = 2,6 núcleos e 50% da memória de 8-GB * 1,3 = memória 5,3-GB), o VM SKU mais barato de quatro núcleos (contagem de núcleo suportado mais próximo) e oito GB de memória (suportado mais próximo) tamanho da memória) é recomendado.
- [Saiba mais](concepts-assessment-calculation.md#types-of-assessments) sobre o tamanho da avaliação.

## <a name="azure-disk-skus-bigger-than-on-premises"></a>SKUs de disco azul maior do que no local

A Avaliação do Servidor Migratório Azure pode recomendar um disco maior com base no tipo de avaliação.
- O dimensionamento do disco na Avaliação do Servidor depende de duas propriedades de avaliação: critérios de dimensionamento e tipo de armazenamento.
- Se os critérios de dimensionamento **forem baseados no desempenho,** e o tipo de armazenamento for definido como **Automático,** o IOPS e os valores de entrada do disco são considerados ao identificar o tipo de disco-alvo (Standard HDD, Standard SSD ou Premium). Recomenda-se então um SKU de disco do tipo de disco, e a recomendação considera os requisitos de tamanho do disco no local.
- Se os critérios de dimensionamento **forem baseados no desempenho,** e o tipo de armazenamento for **Premium,** recomenda-se um disco premium SKU em Azure com base nos requisitos de iopS, de entrada e tamanho do disco no local. A mesma lógica é utilizada para executar o dimensionamento do disco quando os **critérios de** dimensionamento são Como no local e o tipo de armazenamento é **Standard HDD,** **Standard SSD**ou **Premium**.

Como exemplo, se tiver um disco no local com 32 GB de memória, mas o iOPS agregado para o disco é 800 IOPS, a Avaliação do Servidor recomenda um disco premium (devido aos requisitos mais elevados do IOPS), e recomenda um SKU de disco que possa suportar o IOPS necessário e tamanho. A correspondência mais próxima neste exemplo seria P15 (256 GB, IOPS de 1100). Embora o tamanho exigido pelo disco no local fosse de 32 GB, a Avaliação do Servidor recomenda um disco maior devido à elevada exigência de IOPS do disco no local.

## <a name="utilized-corememory-percentage-missing"></a>Percentagem de núcleo/memória utilizada em falta

A Avaliação do Servidor reporta "PercentOfCoresUtilizedMissing" ou "PercentageofMemoryUtilizedMissing" quando o aparelho Azure Migrate não consegue recolher dados de desempenho para os VMs relevantes no local.

- Isto pode ocorrer se os VMs forem desligados durante a duração da avaliação. O aparelho não pode recolher dados de desempenho para um VM quando está desligado.
- Se apenas faltam os contadores de memória e está a tentar avaliar os VMs hiper-V, verifique se tem memória dinâmica ativada nestes VMs. Há um problema conhecido apenas para Os VMs Hiper-V, em que um aparelho Azure Migrate não pode recolher dados de utilização da memória para VMs que não têm memória dinâmica ativada.
- Se faltar algum dos contadores de desempenho, a Avaliação do Servidor Migratório Azure recai sobre os núcleos e memória atribuídos, e recomenda um tamanho VM correspondente.
- Se faltarem todos os balcões de desempenho, certifique-se de que os requisitos de acesso portuário para avaliação são cumpridos. Saiba mais sobre os requisitos de acesso à porta para [VMware,](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#port-access) [Hiper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#port-access) e avaliação do servidor [físico.](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-physical#port-access)

## <a name="is-the-operating-system-license-included"></a>A licença do sistema operativo está incluída?

A Liquidação do Servidor Migratório Azure considera atualmente que a licença do sistema operativo custa apenas às máquinas Windows. Os custos da licença das máquinas Linux não são atualmente considerados.

## <a name="how-does-performance-based-sizing-work"></a>Como funciona o tamanho baseado no desempenho?

A Avaliação do Servidor recolhe continuamente os dados de desempenho das máquinas no local e utiliza-os para recomendar o SKU da VM e o SKU do disco no Azure. [Saiba como](concepts-assessment-calculation.md#calculate-sizing-performance-based) os dados baseados no desempenho são recolhidos.


## <a name="dependency-visualization-in-azure-government"></a>Visualização da dependência no Governo de Azure

O Azure Migrate depende do Mapa de Serviço sintetiza a funcionalidade de visualização da dependência. Como o Mapa de Serviços está atualmente indisponível no Governo do Azure, esta funcionalidade não está disponível no Governo do Azure.

## <a name="dependencies-dont-show-after-agent-install"></a>Dependências não aparecem após instalação de agente

Depois de ter instalado os agentes de visualização da dependência em VMs no local, o Azure Migrate normalmente demora 15 a 30 minutos a mostrar as dependências no portal. Se esperou mais de 30 minutos, certifique-se de que o Microsoft Monitoring Agent (MMA) pode ligar-se ao espaço de trabalho do Log Analytics.

Para VMs windows:
1. No Painel de Controlo, inicie o MMA.
2. No **Microsoft Monitoring Agent properties** > **Azure Log Analytics (OMS)**, certifique-se de que o **Estado** do espaço de trabalho é verde.
3. Se o estado não estiver verde, tente remover o espaço de trabalho e adicioná-lo novamente ao MMA.

    ![Estado do MMA](./media/troubleshoot-assessment/mma-properties.png)

Para os VMs Linux, certifique-se de que os comandos de instalação para MMA e o agente de dependência foram bem sucedidos.

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

- **Agente MMS**: Reveja os sistemas [operativos Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)suportados e [linux.](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)
- **Agente de dependência**: os sistemas [operativos Windows e Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) suportados.

## <a name="visualize-dependencies-for--hour"></a>Visualizar dependências durante > hora

Embora o Azure Migrate lhe permita voltar a uma data específica no último mês, a duração máxima para a qual pode visualizar as dependências é de uma hora.

Por exemplo, pode utilizar a funcionalidade de duração de tempo no mapa de dependência para ver dependências para ontem, mas pode vê-las por um período de uma hora.

No entanto, pode utilizar registos do Monitor Azure para [consultar os dados de dependência](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) durante uma duração mais longa.

## <a name="visualized-dependencies-for--10-machines"></a>Dependências visualizadas para > 10 máquinas

Na Avaliação do Servidor Migratório Azure, pode [visualizar dependências de grupos](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) com até 10 VMs. Para grupos maiores, recomendamos que divida os VMs em grupos menores para visualizar dependências.

## <a name="machines-show-install-agent"></a>As máquinas mostram "Instalar agente"

Após a migração de máquinas com visualização de dependência habilitada para o Azure, as máquinas podem mostrar ação "Instalar agente" em vez de "Ver dependências" devido ao seguinte comportamento:


- Após a migração para Azure, as máquinas no local são desligadas e os VMs equivalentes são fiados em Azure. Estas máquinas adquirem um endereço MAC diferente.
- As máquinas também podem ter um endereço IP diferente, com base em se manteve ou não o endereço IP no local.
- Se os endereços MAC e IP forem diferentes dos no local, a Azure Migrate não associa as máquinas no local a quaisquer dados de dependência do Mapa de Serviço. Neste caso, mostrará a opção de instalar o agente em vez de ver dependências.
- Após uma migração de teste para Azure, as máquinas no local permanecem ligadas como esperado. As máquinas equivalentes fiadas em Azure adquirem diferentes endereços MAC e podem adquirir diferentes endereços IP. A menos que bloqueie o tráfego de registo slog do Azure Monitor destas máquinas, a Azure Migrate não associará as máquinas no local a quaisquer dados de dependência do Mapa de Serviço, e assim mostrará a opção de instalar agentes, em vez de ver dependências.


## <a name="capture-network-traffic"></a>Capturar tráfego de rede

Recolha os registos de tráfego da rede da seguinte forma:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Pressione o F12 para iniciar ferramentas de desenvolvimento. Se necessário, limpe as entradas claras na definição de **navegação.**
3. Selecione o separador **Rede** e comece a capturar o tráfego da rede:
   - No Chrome, **selecione Conservar**. A gravação deve começar automaticamente. Um círculo vermelho indica que o tráfego está a ser capturado. Se o círculo vermelho não aparecer, selecione o círculo preto para iniciar.
   - No Microsoft Edge e no Internet Explorer, a gravação deve começar automaticamente. Se não o fizer, selecione o botão de reprodução verde.
4. Tente reproduzir o erro.
5. Depois de ter encontrado o erro durante a gravação, pare de gravar e guarde uma cópia da atividade gravada:
   - No Chrome, clique à direita e selecione **Guardar como HAR com conteúdo**. Esta ação comprime e exporta os troncos como um ficheiro .har.
   - No Microsoft Edge ou no Internet Explorer, selecione a opção de **tráfego capturado exportada exportada.** Esta ação comprime e exporta o tronco.
6. Selecione o separador **Consolapara** verificar quaisquer avisos ou erros. Para guardar o registo da consola:
   - No Chrome, clique à direita em qualquer lugar do registo da consola. Selecione **Guardar como,** para exportar, e feche o tronco.
   - No Microsoft Edge ou no Internet Explorer, clique nos erros e selecione **Copiar tudo**.
7. Feche as ferramentas de desenvolvimento.

## <a name="next-steps"></a>Passos seguintes

[Criar](how-to-create-assessment.md) ou [personalizar](how-to-modify-assessment.md) uma avaliação.
