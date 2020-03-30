---
title: Como utilizar o PerfInsights Linux no Microsoft Azure. Microsoft Docs
description: Aprende a usar o PerfInsights para resolver problemas de desempenho do Linux VM.
services: virtual-machines-linux'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 7/10/2019
ms.author: genli
ms.openlocfilehash: 19b2fcaed2c80d4ca52ada9f9f0898479e73bcf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266991"
---
# <a name="how-to-use-perfinsights"></a>Como utilizar o PerfInsights

[PerfInsights Linux](https://aka.ms/perfinsightslinuxdownload) é uma ferramenta de diagnóstico de autoajuda que recolhe e analisa os dados de diagnóstico, e fornece um relatório para ajudar a resolver problemas de desempenho da máquina virtual Linux em Azure. PerfInsights pode ser executado em máquinas virtuais suportadas como uma ferramenta autónoma, ou diretamente a partir do portal usando diagnósticos de [desempenho para máquinas virtuais Azure](performance-diagnostics.md).

Se estiver a ter problemas de desempenho com máquinas virtuais, antes de contactar o suporte, execute esta ferramenta.

## <a name="supported-troubleshooting-scenarios"></a>Cenários de resolução de problemas suportados

PerfInsights pode recolher e analisar vários tipos de informação. As seguintes secções abrangem cenários comuns.

### <a name="quick-performance-analysis"></a>Análise rápida do desempenho

Este cenário recolhe informações básicas, tais como armazenamento e configuração de hardware da sua máquina virtual, vários registos, incluindo:

- Informação do Sistema Operativo

- Informações sobre dispositivos PCI

- Registos gerais de osso convidado

- Ficheiros de configuração

- Informações de armazenamento

- Configuração da máquina virtual Azure (recolhida com recurso ao Serviço de [Metadados de Instância](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)Seletiva)

- Lista de processos de execução, utilização de Disco, Memória e CPU

- Informação em rede

Esta é uma recolha passiva de informação que não deve afetar o sistema.

>[!Note]
>O cenário de análise rápida do desempenho é automaticamente incluído em cada um dos seguintes cenários:

### <a name="performance-analysis"></a>Análise de desempenho

Este cenário é semelhante à análise rápida do desempenho, mas permite capturar informações de diagnóstico por uma duração mais longa.

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Que tipo de informação é recolhida pela PerfInsights

São recolhidas informações sobre a máquina virtual Linux, sistema operativo, dispositivos de blocos, consumidores de alto recurso, configuração e vários registos. Veja a seguir mais detalhes:

- Sistema operativo
  - Distribuição e versão linux
  - Informação do kernel
  - Informações sobre o condutor

- Hardware
  - Dispositivos PCI`*`[ ]

- Processos e memória
  - Lista de processos (nome de tarefa, memória utilizada, ficheiros abertos)
  - Total, disponível e memória física gratuita
  - Total, disponível e livre de memória
  - Captura de perfis de CPU e processa utilização de CPU em intervalo de 5 segundos
  - Captura de perfis de utilização da memória de processos em intervalo de 5 segundos

- Redes  
  - Lista de adaptadores de rede com estatísticas de adaptadores
  - Mesa de encaminhamento de rede
  - Portas abertas e estatuto

- Storage
  - Lista de dispositivos de bloco
  - Lista de partições
  - Lista de pontos de montagem
  - Informação sobre volume mDADM
  - Informação sobre volume LVM
  - Captura de perfis em todos os discos ao intervalo de 5 segundos

- Registos
  - /var/log/mensagens
  - /var/log/syslog
  - /var/log/kern.log
  - /var/log/cron.log
  - /var/log/boot.log
  - /var/log/yum.log
  - /var/log/dpkg.log
  - /var/log/cloud-init.log
  - /var/log/cloud-init-output.log
  - /var/log/gpu-manager.log
  - /var/log/waagent.log
  - /var/log/azure/[pasta de\*extensão]/ registo\*
  - /var/opt/microsoft/omsconfig/omsconfig.log
  - /var/opt/microsoft/omsagent/log/omsagent.log
  - /etc/waagent.config
  - Saída de periódico nos últimos cinco dias

- [Metadados de instância de máquina seletiva azul](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

>[!Note]
>[`*`] As informações do IPC ainda não são recolhidas nas distribuições de Debian e SLES

## <a name="run-the-perfinsights-linux-on-your-vm"></a>Execute o Linux PerfInsights no seu VM

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>O que tenho de saber antes de executar a ferramenta?

#### <a name="tool-requirements"></a>Requisitos de ferramentas

- Esta ferramenta deve ser executada no VM que tem o problema de desempenho.
- Python 2.7 deve ser instalado no VM

- As seguintes distribuições são atualmente suportadas:

    | Distribuição               | Versão                                         |
    |----------------------------|-------------------------------------------------|
    | Servidor Oracle Linux        | 6.10`*`[ ], 7.3, 7.6, 7.5 (Oracle-Database-Ee 13.8 imagem de mercado)|
    | CentOS                     | 6.5`*`[ ], 7.6                                    |
    | RHEL                       | 7.2, 7.5, 8.0 [`*`]                               |
    | Ubuntu                     | 14.04, 16.04, 18.04                               |
    | Debian                     | 8, 9, 10 [`*`]                                    |
    | SLES                       | 12 SP4`*`[ ]                                      |
    |                            |                                                   |

>[!Note]
>[`*`] Por favor, consulte a secção [de questões conhecidas](#known-issues)

### <a name="known-issues"></a>Problemas conhecidos

- O RHEL 8 não tem python instalado por defeito. Para executar perfInsights Linux, você deve primeiro instalar Python 2.7

- A recolha de informações do Agente Convidado pode falhar no CentOS 6.x

- As informações sobre dispositivos PCI não são recolhidas nas distribuições baseadas em Debian

- A informação da LVM é parcialmente recolhida em algumas distribuições

### <a name="how-do-i-run-perfinsights"></a>Como faço a PerfInsights

Pode executar perfInsights numa máquina virtual instalando diagnósticos de desempenho azure a partir do portal Azure. Também pode executá-lo como uma ferramenta autónoma.

>[!Note]
>PerfInsights simplesmente recolhe e analisa os dados. Não faz modificações no sistema.

#### <a name="install-and-run-perfinsights-from-the-azure-portal"></a>Instale e execute perfInsights a partir do portal Azure

Para mais informações sobre esta opção, consulte [O Diagnóstico de Desempenho do Azure](performance-diagnostics.md).  

#### <a name="run-perfinsights-in-standalone-mode"></a>Executar PerfInsights em modo autónomo

Para executar a ferramenta PerfInsights, siga estes passos:

1. Baixe [PerfInsights.tar.gz](https://aka.ms/perfinsightslinuxdownload) para uma pasta na sua máquina virtual e extraio o conteúdo utilizando os comandos abaixo do terminal.

   ```bash
   wget https://download.microsoft.com/download/9/F/8/9F80419C-D60D-45F1-8A98-718855F25722/PerfInsights.tar.gz
   ```

   ```bash
   tar xzvf PerfInsights.tar.gz
   ```

2. Navegue para a `perfinsights.py` pasta que `perfinsights.py` contém ficheiro e, em seguida, corra para ver os parâmetros de linha de comando disponíveis.

    ```bash
    cd <the path of PerfInsights folder>
    sudo python perfinsights.py
    ```

    ![Screenshot da saída da linha de comando PerfInsights Linux](media/how-to-use-perfinsights-linux/perfinsights-linux-commandline.png)

    A sintaxe básica para executar cenários PerfInsights é:

    ```bash
    sudo python perfinsights.py -r <ScenarioName> -d [duration]<H | M | S> [AdditionalOptions]
    ```

    Pode utilizar o exemplo abaixo para executar um cenário de análise de desempenho rápido durante 1 minuto e criar os resultados em /tmp/pasta de saída:

    ```bash
    sudo python perfinsights.py -r quick -d 1M -a -o /tmp/output
    ```

    Pode usar o exemplo abaixo para executar o cenário de análise de desempenho durante 5 minutos e enviar a bola de alcatrão para a conta de armazenamento:

    ```bash
    sudo python perfinsights.py -r vmslow -d 300S -a -t <StorageAccountName> -k <StorageAccountKey> -i <full resource Uri of the current VM>
    ```

    >[!Note]
    >Antes de executar um cenário, a PerfInsights leva o utilizador a concordar em partilhar informações de diagnóstico e a concordar com o EULA. Utilize **-a ou -aceitar-aceitar-isenção-aviso-diagnóstico-e-share-diagnostics** para ignorar estas solicitações.
    >
    >Se tiver um bilhete de suporte ativo com a Microsoft e executar o PerfInsights por pedido do engenheiro de suporte com o qual está a trabalhar, certifique-se de fornecer o número do bilhete de suporte utilizando a opção **-s ou --pedido de suporte.**

Quando o executo estiver concluído, um novo ficheiro de alcatrão aparece na mesma pasta que o PerfInsights, a menos que não seja especificada nenhuma pasta de saída. O nome do ficheiro é **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.tar.gz.** Pode enviar este ficheiro ao agente de suporte para análise ou abrir o relatório dentro do ficheiro para analisar as conclusões e recomendações.

## <a name="review-the-diagnostics-report"></a>Reveja o relatório de diagnóstico

Dentro do ficheiro **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.tar.gz,** pode encontrar um relatório HTML que detalha as conclusões da PerfInsights. Para rever o relatório, expanda o ficheiro **\_PerformanceDiagnostics\_yyyy-MM-dd hh-mm-ss-fff.tar.gz** e, em seguida, abra o ficheiro **PerfInsights Report.html.**

### <a name="overview-tab"></a>Separador de visão geral

O separador **Overview** fornece detalhes básicos de execução e informações de máquinas virtuais. O separador **Resultados** apresenta um resumo das recomendações de todas as diferentes secções do relatório PerfInsights.

![Screenshot do Relatório PerfInsights](media/how-to-use-perfinsights-linux/perfinsights-linux-overview.png)  
![Screenshot do Relatório PerfInsights](media/how-to-use-perfinsights-linux/perfinsights-linux-findings-tab.png)

> [!NOTE]
> As descobertas classificadas como altas são questões conhecidas que podem causar problemas de desempenho. As descobertas categorizadas como médias representam configurações não ótimas que não causam necessariamente problemas de desempenho. Os resultados classificados como baixos são apenas declarações informativas.

Reveja as recomendações e os links para todas as conclusões altas e médias. Saiba como podem afetar o desempenho, e também sobre as melhores práticas para configurações otimizadas pelo desempenho.

### <a name="cpu-tab"></a>Separador CPU

**O separador CPU** fornece informações sobre o consumo de CPU em todo o sistema durante a execução PerfInsights. Informações sobre os elevados períodos de utilização do CPU e os consumidores de CPU de longo prazo serão úteis para resolver problemas elevados relacionados com a CPU.

![Screenshot do separador PerfInsights Report CPU](media/how-to-use-perfinsights-linux/perfinsights-linux-cpu-tab.png)

### <a name="storage-tab"></a>Separador de armazenamento

A secção **Resultados** apresenta várias conclusões e recomendações relacionadas com o armazenamento.

Os **dispositivos de bloco e** outras secções relacionadas, tais como **divisórias,** **LVM,** e separadores **MDADM** descrevem como os dispositivos de bloco são configurados e relacionados uns com os outros.

![Screenshot do separador de armazenamento](media/how-to-use-perfinsights-linux/perfinsights-linux-storage-tab.png)  
![Screenshot do separador MDADM](media/how-to-use-perfinsights-linux/perfinsights-linux-mdadm-config.png)

### <a name="linux-tab"></a>Separador Linux

O separador **Linux** contém informações sobre o hardware e o sistema operativo em funcionamento no seu VM. Os detalhes incluem uma lista de processos de execução e informações sobre os controladores de Agente Convidado, CPI, CPU, Motoristas e LIS.

![Screenshot do separador Linux](media/how-to-use-perfinsights-linux/perfinsights-linux-tab.png)

## <a name="next-steps"></a>Passos seguintes

Pode fazer o upload de registos e relatórios de diagnóstico para o Microsoft Support para posterior revisão. Quando trabalha com o pessoal do Microsoft Support, podem solicitar que transmita a saída gerada pela PerfInsights para ajudar no processo de resolução de problemas.

A imagem que se segue mostra uma mensagem semelhante à que poderá receber:

![Screenshot da mensagem da amostra do Suporte da Microsoft](media/how-to-use-perfinsights-linux/support-email.png)

Siga as instruções na mensagem para aceder ao espaço de trabalho de transferência de ficheiros. Para obter segurança adicional, tem de alterar a sua palavra-passe na primeira utilização.

Depois de iniciar sessão, encontrará uma caixa de diálogo para fazer o upload do ficheiro **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.tar.gz** que foi recolhido pela PerfInsights.
