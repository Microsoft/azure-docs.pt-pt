---
title: Solucionar problemas de visualização de dependência e de avaliação em migrações para Azure
description: Obtenha ajuda para solucionar problemas de visualização de dependência e de avaliação em migrações para Azure.
ms.service: azure-migrate
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/02/2020
ms.openlocfilehash: fb1e32d9f67febb09eadfb5d31221db33504eb05
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289480"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>Resolver problemas de avaliação/visualização de dependência

Este artigo ajuda você a solucionar problemas com a visualização de dependência e avaliação com as [migrações para Azure: avaliação de servidor](migrate-services-overview.md#azure-migrate-server-assessment-tool).


## <a name="assessment-readiness-issues"></a>Problemas de preparação da avaliação

Corrija os problemas de preparação da avaliação da seguinte maneira:

**Problema** | **Soluciona**
--- | ---
Tipo de inicialização sem suporte | O Azure não dá suporte a VMs com um tipo de inicialização EFI. Recomendamos que você converta o tipo de inicialização para BIOS antes de executar uma migração. <br/><br/>Você pode usar a migração de servidor de migrações para o Azure para lidar com a migração dessas VMs. Ele converterá o tipo de inicialização da VM para BIOS durante a migração.
Sistema operacional Windows com suporte condicional | O sistema operacional passou pela data de fim do suporte e precisa de um contrato de suporte personalizado (CSA) para [dar suporte no Azure](https://aka.ms/WSosstatement). Considere a atualização antes de migrar para o Azure.
Sistema operacional Windows sem suporte | O Azure dá suporte apenas a [versões selecionadas do sistema operacional Windows](https://aka.ms/WSosstatement). Considere atualizar o computador antes de migrar para o Azure.
Sistema operacional Linux endossado condicionalmente | O Azure endossa apenas [as versões selecionadas do sistema operacional Linux](../virtual-machines/linux/endorsed-distros.md). Considere atualizar o computador antes de migrar para o Azure.
Sistema operacional Linux não endossado | O computador pode iniciar no Azure, mas o Azure não fornece suporte ao sistema operacional. Considere atualizar para uma [versão do Linux endossada](../virtual-machines/linux/endorsed-distros.md) antes de migrar para o Azure.
Sistema operacional desconhecido | O sistema operacional da VM foi especificado como "other" em vCenter Server. Esse comportamento impede que as migrações para Azure verifiquem a prontidão do Azure da VM. Verifique se o sistema operacional tem [suporte](https://aka.ms/azureoslist) do Azure antes de migrar o computador.
Versão de bits sem suporte | As VMs com sistemas operacionais de 32 bits podem ser inicializadas no Azure, mas recomendamos que você atualize para 64 bits antes de migrar para o Azure.
Requer uma assinatura Microsoft Visual Studio | O computador está executando um sistema operacional cliente Windows, que tem suporte apenas por meio de uma assinatura do Visual Studio.
VM não encontrada para o desempenho de armazenamento necessário | O desempenho de armazenamento (operações de entrada/saída por segundo [IOPS] e taxa de transferência) necessários para o computador excede o suporte à VM do Azure. Reduza os requisitos de armazenamento para a máquina antes da migração.
VM não encontrada para o desempenho de rede necessário | O desempenho de rede (entrada/saída) necessário para o computador excede o suporte de VM do Azure. Reduza os requisitos de rede para o computador.
VM não encontrada no local especificado | Use um local de destino diferente antes da migração.
Um ou mais discos inadequados | Um ou mais discos anexados à VM não atendem aos requisitos do Azure. Um<br/><br/> Migrações para Azure: a avaliação do servidor atualmente não dá suporte a discos SSD Ultra e avalia os discos com base nos limites de disco para discos gerenciados Premium (32 TB).<br/><br/> Para cada disco anexado à VM, verifique se o tamanho do disco é < 64 TB (com suporte de discos SSD Ultra).<br/><br/> Se não estiver, reduza o tamanho do disco antes de migrar para o Azure, ou use vários discos no Azure e distribua [-os juntos](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) para obter limites de armazenamento maiores. Certifique-se de que o desempenho (IOPS e taxa de transferência) necessário para cada disco tem suporte dos [discos de máquina virtual gerenciada](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits)pelo Azure.
Um ou mais adaptadores de rede inadequados. | Remova adaptadores de rede não utilizados do computador antes da migração.
A contagem de discos excede o limite | Remova os discos não utilizados do computador antes da migração.
O tamanho do disco excede o limite | Migrações para Azure: a avaliação do servidor atualmente não dá suporte a discos SSD Ultra e avalia os discos com base nos limites de disco Premium (32 TB).<br/><br/> No entanto, o Azure dá suporte a discos com tamanho de até 64 TB (com suporte de discos SSD Ultra). Reduza os discos para menos de 64 TB antes da migração ou use vários discos no Azure e distribua [-os juntos](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) para obter limites de armazenamento mais altos.
Disco não disponível no local especificado | Verifique se o disco está em seu local de destino antes de migrar.
Disco não disponível para a redundância especificada | O disco deve usar o tipo de armazenamento de redundância definido nas configurações de avaliação (LRS por padrão).
Não foi possível determinar a adequação do disco devido a um erro interno | Tente criar uma nova avaliação para o grupo.
VM com núcleos necessários e memória não encontrada | O Azure não pôde encontrar um tipo de VM adequado. Reduza a memória e o número de núcleos do computador local antes de migrar.
Não foi possível determinar a adequação da VM devido a um erro interno | Tente criar uma nova avaliação para o grupo.
Não foi possível determinar a adequação para um ou mais discos devido a um erro interno | Tente criar uma nova avaliação para o grupo.
Não foi possível determinar a adequação para um ou mais adaptadores de rede devido a um erro interno | Tente criar uma nova avaliação para o grupo.

## <a name="linux-vms-are-conditionally-ready"></a>As VMs do Linux estão "prontamente prontas"

A avaliação do servidor marca as VMs do Linux como "prontamente prontas" devido a uma lacuna conhecida na avaliação do servidor.

- A lacuna impede que ele detecte a versão secundária do sistema operacional Linux instalada nas VMs locais.
- Por exemplo, para RHEL 6,10, atualmente a avaliação do servidor detecta apenas RHEL 6 como a versão do sistema operacional.
-  Como o Azure endossa apenas versões específicas do Linux, as VMs do Linux estão atualmente marcadas como prontas para avaliação de servidor.
- Você pode determinar se o SO Linux em execução na VM local é endossado no Azure examinando o [suporte do Azure Linux](https://aka.ms/migrate/selfhost/azureendorseddistros).
-  Depois de verificar a distribuição endossada, você pode ignorar este aviso.

## <a name="azure-skus-bigger-than-on-premises"></a>SKUs do Azure maiores do que o local

A avaliação de servidor de migrações para Azure pode recomendar SKUs de VM do Azure com mais núcleos e memória do que a alocação local atual com base no tipo de avaliação:


- A recomendação de SKU de VM depende das propriedades de avaliação.
- Isso é afetado pelo tipo de avaliação que você executa na avaliação do servidor: *baseada em desempenho*ou *como local*.
- Para avaliações baseadas em desempenho, a avaliação do servidor considera os dados de utilização das VMs locais (CPU, memória, disco e utilização de rede) para determinar o SKU de VM de destino correto para suas VMs locais. Ele também adiciona um fator de conforto ao determinar a utilização efetiva.
- Para o dimensionamento local, os dados de desempenho não são considerados e o SKU de destino é recomendado com base na alocação local.

Para mostrar como isso pode afetar as recomendações, vamos usar um exemplo:

Temos uma VM local com quatro núcleos e oito GB de memória, com 50% de utilização da CPU e 50% de utilização da memória e um fator de conforto especificado de 1,3.

-  Se a avaliação for **como local**, é recomendável um SKU de VM do Azure com quatro núcleos e 8 GB de memória.
- Se a avaliação for baseada em desempenho, com base na utilização efetiva de CPU e memória (50% de 4 núcleos * 1,3 = 2,6 núcleos e 50% de 8 GB de memória * 1,3 = 5,3 GB de memória), o SKU de VM mais barato de quatro núcleos (contagem de núcleos com suporte mais próximo) e oito GB de memória (com suporte mais próximo tamanho da memória) é recomendado.
- [Saiba mais](concepts-assessment-calculation.md#assessments-in-server-assessment) sobre o dimensionamento da avaliação.

## <a name="azure-disk-skus-bigger-than-on-premises"></a>SKUs de disco do Azure maiores do que localmente

A avaliação de servidor de migrações para Azure pode recomendar um disco maior com base no tipo de avaliação.
- O dimensionamento de disco na avaliação do servidor depende de duas propriedades de avaliação: critérios de dimensionamento e tipo de armazenamento.
- Se os critérios de dimensionamento forem **baseados em desempenho**e o tipo de armazenamento for definido como **automático**, os valores de IOPS e taxa de transferência do disco serão considerados ao identificar o tipo de disco de destino (HDD Standard, SSD Standard ou Premium). Um SKU de disco do tipo de disco é recomendado, e a recomendação considera os requisitos de tamanho do disco local.
- Se os critérios de dimensionamento forem **baseados em desempenho**e o tipo de armazenamento for **Premium**, um SKU de disco Premium no Azure será recomendado com base nos requisitos de IOPS, taxa de transferência e tamanho do disco local. A mesma lógica é usada para executar o dimensionamento de disco quando os critérios de dimensionamento são **locais** e o tipo de armazenamento é **HDD Standard**, **SSD Standard**ou **Premium**.

Por exemplo, se você tiver um disco local com 32 GB de memória, mas o IOPS agregado de leitura e gravação para o disco for de 800 IOPS, a avaliação do servidor recomendará um disco Premium (devido aos requisitos de IOPS mais altos) e, em seguida, recomendará uma SKU de disco que possa dar suporte ao r IOPS e tamanho de ecessário. A correspondência mais próxima neste exemplo seria P15 (256 GB, IOPS de 1100). Embora o tamanho exigido pelo disco local tenha 32 GB, a avaliação do servidor recomenda um disco maior devido ao requisito de IOPS alto do disco local.

## <a name="utilized-corememory-percentage-missing"></a>Porcentagem de memória/núcleo utilizada ausente

A avaliação do servidor relata "PercentageOfCoresUtilizedMissing" ou "PercentageOfMemoryUtilizedMissing" quando o dispositivo de migrações para Azure não pode coletar dados de desempenho para as VMs locais relevantes.

- Isso pode ocorrer se as VMs estiverem desativadas durante a duração da avaliação. O dispositivo não pode coletar dados de desempenho para uma VM quando ela está desativada.
- Se apenas os contadores de memória estiverem ausentes e você estiver tentando avaliar as VMs do Hyper-V, verifique se você tem memória dinâmica habilitada nessas VMs. Há um problema conhecido apenas para VMs do Hyper-V, em que um dispositivo de migrações para Azure não pode coletar dados de utilização de memória para VMs que não têm memória dinâmica habilitada.
- Se algum dos contadores de desempenho estiver ausente, a avaliação do servidor de migrações para Azure voltará para os núcleos e a memória alocados e recomendará um tamanho de VM correspondente.
- Se todos os contadores de desempenho estiverem ausentes, verifique se os requisitos de acesso à porta para avaliação foram atendidos. Saiba mais sobre os requisitos de acesso de porta para [VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#port-access), [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#port-access) e avaliação de servidor [físico](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-physical#port-access) .

## <a name="is-the-operating-system-license-included"></a>A licença do sistema operacional está incluída?

A avaliação de servidor de migrações para Azure atualmente considera o custo de licença do sistema operacional somente para computadores Windows. Os custos de licença para computadores Linux não são atualmente considerados.

## <a name="how-does-performance-based-sizing-work"></a>Como funciona o dimensionamento baseado em desempenho?

A Avaliação do Servidor recolhe continuamente os dados de desempenho das máquinas no local e utiliza-os para recomendar o SKU da VM e o SKU do disco no Azure. [Saiba como](concepts-assessment-calculation.md#calculate-sizing-performance-based) os dados baseados em desempenho são coletados.


## <a name="dependency-visualization-in-azure-government"></a>Visualização de dependência no Azure governamental

As migrações para Azure dependem Mapa do Serviço para a funcionalidade de visualização de dependência. Como Mapa do Serviço não está disponível no Azure governamental no momento, essa funcionalidade não está disponível no Azure governamental.

## <a name="dependencies-dont-show-after-agent-install"></a>As dependências não são mostradas após a instalação do agente

Depois de instalar os agentes de visualização de dependência em VMs locais, as migrações para Azure normalmente leva 15-30 minutos para exibir as dependências no Portal. Se você tiver aguardado por mais de 30 minutos, verifique se o Microsoft Monitoring Agent (MMA) pode se conectar ao espaço de trabalho Log Analytics.

Para VMs do Windows:
1. No painel de controle, inicie o MMA.
2. Nas **Propriedades de Microsoft Monitoring Agent** > **log Analytics do Azure (OMS)** , verifique se o **status** do espaço de trabalho está verde.
3. Se o status não for verde, tente remover o espaço de trabalho e adicioná-lo novamente ao MMA.

    ![Status do MMA](./media/troubleshoot-assessment/mma-properties.png)

Para VMs do Linux, certifique-se de que os comandos de instalação para MMA e o Dependency Agent tenham êxito.

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

- **Agente MMS**: examine os sistemas operacionais [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)e [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) com suporte.
- **Agente de dependência**: os sistemas operacionais [Windows e Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) com suporte.

## <a name="visualize-dependencies-for--hour"></a>Visualizar dependências por > hora

Embora as migrações para Azure permitam que você volte para uma data específica no último mês, a duração máxima para a qual você pode visualizar as dependências é de uma hora.

Por exemplo, você pode usar a funcionalidade duração de tempo no mapa de dependências para exibir dependências de ontem, mas você pode exibi-las somente para um período de uma hora.

No entanto, você pode usar os logs de Azure Monitor para [consultar os dados de dependência](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) por uma duração maior.

## <a name="visualized-dependencies-for--10-machines"></a>Dependências visualizadas para computadores > 10

Na avaliação do servidor de migrações para Azure, você pode [Visualizar dependências para grupos](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) com até 10 VMS. Para grupos maiores, recomendamos que você divida as VMs em grupos menores para visualizar as dependências.

## <a name="machines-show-install-agent"></a>Computadores mostram "instalar agente"

Após a migração de computadores com a visualização de dependência habilitada para o Azure, as máquinas podem mostrar a ação "instalar agente" em vez de "Exibir dependências" devido ao seguinte comportamento:


- Após a migração para o Azure, as máquinas locais são desativadas e as VMs equivalentes são giradas no Azure. Esses computadores adquirem um endereço MAC diferente.
- Os computadores também podem ter um endereço IP diferente, dependendo se você manteve o endereço IP local ou não.
- Se os endereços MAC e IP forem diferentes do local, as migrações para Azure não associarão os computadores locais a qualquer Mapa do Serviço dados de dependência. Nesse caso, ele mostrará a opção para instalar o agente em vez de exibir as dependências.
- Após uma migração de teste para o Azure, as máquinas locais permanecem ativadas conforme o esperado. As máquinas equivalentes giradas no Azure adquirem um endereço MAC diferente e podem adquirir endereços IP diferentes. A menos que você bloqueie o tráfego de log Azure Monitor de saída desses computadores, as migrações para Azure não associarão os computadores locais a qualquer Mapa do Serviço dados de dependência e, portanto, mostrarão a opção de instalar agentes, em vez de exibir dependências.


## <a name="capture-network-traffic"></a>Capturar o tráfego de rede

Colete os logs de tráfego de rede da seguinte maneira:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Pressione F12 para iniciar a Ferramentas para Desenvolvedores. Se necessário, desmarque a configuração **Limpar entradas na navegação** .
3. Selecione a guia **rede** e inicie a captura do tráfego de rede:
   - No Chrome, selecione **preservar log**. A gravação deve ser iniciada automaticamente. Um círculo vermelho indica que o tráfego está sendo capturado. Se o círculo vermelho não aparecer, selecione o círculo preto para iniciar.
   - No Microsoft Edge e no Internet Explorer, a gravação deve ser iniciada automaticamente. Se não estiver, selecione o botão verde Play.
4. Tente reproduzir o erro.
5. Depois de encontrar o erro durante a gravação, interrompa a gravação e salve uma cópia da atividade gravada:
   - No Chrome, clique com o botão direito do mouse e selecione **salvar como Har com conteúdo**. Essa ação compacta e exporta os logs como um arquivo. Har.
   - No Microsoft Edge ou Internet Explorer, selecione a opção **Exportar tráfego capturado** . Essa ação compacta e exporta o log.
6. Selecione a guia **console** para verificar se há avisos ou erros. Para salvar o log do console:
   - No Chrome, clique com o botão direito do mouse em qualquer lugar no log do console. Selecione **salvar como**, para exportar e compactar o log.
   - No Microsoft Edge ou no Internet Explorer, clique com o botão direito do mouse nos erros e selecione **copiar tudo**.
7. Feche Ferramentas para Desenvolvedores.

## <a name="next-steps"></a>Passos seguintes

[Crie](how-to-create-assessment.md) ou [Personalize](how-to-modify-assessment.md) uma avaliação.
