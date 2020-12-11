---
title: Como utilizar o PerfInsights Linux no Microsoft Azure Microsoft Docs
description: Aprende a usar PerfInsights para resolver problemas de desempenho do Linux VM.
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
ms.openlocfilehash: 340164ef86d34f273b3d5a98b62300bc6cc50df7
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109424"
---
# <a name="how-to-use-perfinsights"></a>Como utilizar o PerfInsights

[PerfInsights Linux](https://aka.ms/perfinsightslinuxdownload) é uma ferramenta de diagnóstico de autoajuda que recolhe e analisa os dados de diagnóstico, e fornece um relatório para ajudar a resolver problemas de desempenho da máquina virtual Linux em Azure. PerfInsights pode ser executado em máquinas virtuais suportadas como uma ferramenta autónoma, ou diretamente do portal, utilizando diagnósticos de [desempenho para máquinas virtuais Azure](performance-diagnostics.md).

Se tiver problemas de desempenho com máquinas virtuais, antes de contactar o suporte, execute esta ferramenta.

## <a name="supported-troubleshooting-scenarios"></a>Cenários de resolução de problemas apoiados

PerfInsights pode recolher e analisar vários tipos de informação. As seguintes secções abrangem cenários comuns.

### <a name="quick-performance-analysis"></a>Análise rápida do desempenho

Este cenário recolhe informações básicas como armazenamento e configuração de hardware da sua máquina virtual, vários registos, incluindo:

- Informações do Sistema Operativo

- Informações sobre dispositivos PCI

- Registos geral do SO convidado

- Ficheiros de configuração

- Informação de armazenamento

- Configuração da máquina virtual Azure (recolhida usando [o Serviço de Metadados de Instância Azure](../windows/instance-metadata-service.md))

- Lista de processos de execução, utilização de disco, memória e CPU

- Informação de rede

Esta é uma recolha passiva de informação que não deve afetar o sistema.

>[!Note]
>O cenário de análise rápida do desempenho é automaticamente incluído em cada um dos seguintes cenários:

### <a name="performance-analysis"></a>Análise de desempenho

Este cenário é semelhante à análise rápida do desempenho, mas permite capturar informações de diagnóstico por uma duração mais longa.

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Que tipo de informação é recolhida pela PerfInsights

São recolhidas informações sobre a máquina virtual Linux, sistema operativo, dispositivos de bloco, consumidores de alto recurso, configuração e vários registos. Veja a seguir mais detalhes:

- Sistema operativo
  - Distribuição e versão Linux
  - Informação kernel
  - Informações do condutor

- Hardware
  - Dispositivos PCI `*` [ ]

- Processos e memória
  - Lista de processos (nome da tarefa, memória utilizada, ficheiros abertos)
  - Total, disponível e livre memória física
  - Memória total, disponível e livre de troca
  - Captura de perfis de CPU e processa utilização do CPU em intervalo de 5 segundos
  - Captura de perfis de utilização da memória dos processos com intervalo de 5 segundos

- Redes  
  - Lista de adaptadores de rede com estatísticas de adaptadores
  - Tabela de encaminhamento de rede
  - Portas abertas e estatuto

- Armazenamento
  - Lista de dispositivos de bloqueio
  - Lista de divisórias
  - Lista de pontos de montagem
  - Informação de volume do MDADM
  - Informação de volume LVM
  - Captura de perfis em todos os discos com intervalo de 5 segundos

- Registos
  - /var/log/mensagens
  - /var/log/syslog
  - /var/log/kern.log
  - /var/log/cron.log
  - /var/log/boot.log
  - /var/log/yum.log
  - /var/log/dpkg.log
  - /var/log/sysstat ou /var/log/sa [ `**` ]
  - /var/log/cloud-init.log
  - /var/log/cloud-init-output.log
  - /var/log/gpu-manager.log
  - /var/log/waagent.log
  - /var/log/azure/[pasta de extensão]/ \* log\*
  - /var/opt/microsoft/omsconfig/omsconfig.log
  - /var/opt/microsoft/omsagent/log/omsagent.log
  - /etc/waagent.config
  - Produção de diário nos últimos cinco dias

- [Metadados de caso de máquina virtual Azure](../windows/instance-metadata-service.md)

>[!Note]
>[ `*` ] As informações sobre os PCI ainda não são recolhidas nas distribuições de Debian e SLES.
> 
>[ `**` ] [ var/log/sysstat ou /var/log/sa contém os ficheiros Relatório de Atividade do Sistema (SAR) que são recolhidos pelo pacote sisstat. Se o pacote de sysstat não for instalado no VM, a ferramenta PerfInsights fornece uma recomendação para a instalar.

## <a name="run-the-perfinsights-linux-on-your-vm"></a>Executar o PerfInsights Linux no seu VM

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>O que tenho de saber antes de executar a ferramenta?

#### <a name="tool-requirements"></a>Requisitos de ferramentas

- Esta ferramenta deve ser executada no VM que tem o problema de desempenho.
- Python 3.x ou Python 2.7 devem ser instalados no VM.

- As seguintes distribuições são atualmente suportadas:

    | Distribuição               | Versão                                         |
    |----------------------------|-------------------------------------------------|
    | Servidor Oracle Linux        | 6.10 [ `*` ], 7.3, 7.5, 7.6, 7.7, 7.8 |
    | CentOS                     | 6.5 [ `*` ], 7.6, 7.7, 7.8                                    |
    | RHEL                       | 7.2, 7.5, 8.0 `*` , 8.1, 8.2                               |
    | Ubuntu                     | 14.04, 16.04, 18.04, 20.04                               |
    | Debian                     | 8, 9, 10 `*` .                                    |
    | SLES                       | 12 SP4 [ `*` ], 12 SP5 [ `*` ], 15 [ `*` ], 15 SP1 [ `*` ], 15 SP2 [ `*` ]                                      |
    |                            |                                                   |

>[!Note]
>[ `*` ] Por favor, consulte a secção de [questões conhecidas](#known-issues)

### <a name="known-issues"></a>Problemas conhecidos

- O RHEL 8 não tem python instalado por defeito. Para executar PerfInsights Linux, você deve primeiro instalar Python 2.7

- A recolha de informações do Agente Convidado pode falhar no CentOS 6.x

- As informações sobre dispositivos PCI não são recolhidas nas distribuições baseadas em Debian

- A informação LVM é parcialmente recolhida em algumas distribuições

### <a name="how-do-i-run-perfinsights"></a>Como faço perfInsights

Pode executar PerfInsights numa máquina virtual instalando Azure Performance Diagnostics a partir do portal Azure. Também pode executá-lo como uma ferramenta autónoma.

>[!Note]
>PerfInsights simplesmente recolhe e analisa os dados. Não faz modificações no sistema.

#### <a name="install-and-run-perfinsights-from-the-azure-portal"></a>Instale e execute PerfInsights a partir do portal Azure

Para obter mais informações sobre esta opção, consulte [a Azure Performance Diagnostics](performance-diagnostics.md).  

#### <a name="run-perfinsights-in-standalone-mode"></a>Executar PerfInsights em modo autónomo

Para executar a ferramenta PerfInsights, siga estes passos:

1. Descarregue [PerfInsights.tar.gz](https://aka.ms/perfinsightslinuxdownload) para uma pasta na sua máquina virtual e extraia o conteúdo utilizando os comandos abaixo do terminal.

   ```bash
   wget https://download.microsoft.com/download/9/F/8/9F80419C-D60D-45F1-8A98-718855F25722/PerfInsights.tar.gz
   ```

   ```bash
   tar xzvf PerfInsights.tar.gz
   ```

2. Navegue para a pasta que contém `perfinsights.py` ficheiro e, em seguida, corra `perfinsights.py` para ver os parâmetros de linha de comando disponíveis.

    ```bash
    cd <the path of PerfInsights folder>
    sudo python perfinsights.py
    ```

    ![Screenshot da saída da linha de comando PerfInsights Linux](media/how-to-use-perfinsights-linux/perfinsights-linux-commandline.png)

    A sintaxe básica para executar cenários PerfInsights é:

    ```bash
    sudo python perfinsights.py -r <ScenarioName> -d [duration]<H | M | S> [AdditionalOptions]
    ```

    Pode utilizar o exemplo abaixo para executar um cenário de análise rápida de desempenho durante 1 minuto e criar os resultados na pasta /tmp/saída:

    ```bash
    sudo python perfinsights.py -r quick -d 1M -a -o /tmp/output
    ```

    Pode utilizar o exemplo abaixo para executar o cenário de análise de desempenho durante 5 minutos e carregar a bola de alcatrão de resultados para a conta de armazenamento:

    ```bash
    sudo python perfinsights.py -r vmslow -d 300S -a -t <StorageAccountName> -k <StorageAccountKey> -i <full resource Uri of the current VM>
    ```

    >[!Note]
    >Antes de executar um cenário, a PerfInsights solicita ao utilizador que aceite partilhar informações de diagnóstico e concordar com a EULA. Utilize a opção **de diagnóstico de isenção de isenção ou partilha** para saltar estas indicações.
    >
    >Se tiver um bilhete de suporte ativo com a Microsoft e executar PerfInsights a pedido do engenheiro de suporte com o qual está a trabalhar, certifique-se de fornecer o número do bilhete de suporte utilizando a opção **de pedido de apoio ou -s.**

Quando a execução estiver concluída, aparece uma nova pasta de alcatrão na mesma pasta que o PerfInsights, a menos que não seja especificada nenhuma pasta de saída. O nome do ficheiro é **PerformanceDiagnostics \_ yyyy-MM-dd \_ hh-mm-ss-fff.tar.gz.** Pode enviar este ficheiro ao agente de suporte para análise ou abrir o relatório dentro do ficheiro para rever as conclusões e recomendações.

## <a name="review-the-diagnostics-report"></a>Reveja o relatório de diagnósticos

Dentro do ficheiro **PerformanceDiagnostics \_ yy-MM-dd \_ hh-mm-ss-fff.tar.gz** pode encontrar um relatório HTML que detalha as conclusões do PerfInsights. Para rever o relatório, expanda o ficheiro **PerformanceDiagnostics \_ yyyy-MM-mm-mm-ss-fff.tar.gz \_ e,** em seguida, abra o ficheiro **PerfInsights Report.html.**

### <a name="overview-tab"></a>Separador de visão geral

O **separador Visão** Geral fornece detalhes básicos de execução e informações de máquina virtual. O separador **Resultados** apresenta um resumo das recomendações de todas as diferentes secções do relatório PerfInsights.

![Screenshot do separador visão geral do Relatório PerfInsights.](media/how-to-use-perfinsights-linux/perfinsights-linux-overview.png)  
![Screenshot do separador Linux do Relatório PerfInsights.](media/how-to-use-perfinsights-linux/perfinsights-linux-findings-tab.png)

> [!NOTE]
> As descobertas classificadas como altas são questões conhecidas que podem causar problemas de desempenho. As descobertas classificadas como médias representam configurações não ótimas que não causam necessariamente problemas de desempenho. Os resultados classificados como baixos são apenas declarações informativas.

Reveja as recomendações e ligações para todas as conclusões de alto e médio. Saiba como podem afetar o desempenho, e também sobre as melhores práticas para configurações otimizadas pelo desempenho.

### <a name="cpu-tab"></a>Separador CPU

O separador **CPU** fornece informações sobre o consumo de CPU em todo o sistema durante a execução perfInsights. As informações sobre os elevados períodos de utilização do CPU e os consumidores de CPU de longo prazo serão úteis para resolver problemas elevados relacionados com a CPU.

![Screenshot do separador CPU do Relatório PerfInsights](media/how-to-use-perfinsights-linux/perfinsights-linux-cpu-tab.png)

### <a name="storage-tab"></a>Separador de armazenamento

A secção **Resultados** apresenta várias conclusões e recomendações relacionadas com o armazenamento.

Os **dispositivos de bloqueio** e outras secções relacionadas, tais como **partições, separadores** **LVM** e **MDADM** descrevem como os dispositivos de bloqueio são configurados e relacionados entre si.

![Screenshot do separador de armazenamento](media/how-to-use-perfinsights-linux/perfinsights-linux-storage-tab.png)  
![Screenshot do separador MDADM](media/how-to-use-perfinsights-linux/perfinsights-linux-mdadm-config.png)

### <a name="linux-tab"></a>Separador Linux

O separador **Linux** contém informações sobre o hardware e o sistema operativo em execução no seu VM. Os detalhes incluem uma lista de processos de execução e informações sobre agentes convidados, PCI, CPU, Motoristas e motoristas LIS.

![Screenshot do separador Linux](media/how-to-use-perfinsights-linux/perfinsights-linux-tab.png)

## <a name="next-steps"></a>Passos seguintes

Pode fazer o upload de registos e relatórios de diagnóstico para o Microsoft Support para posterior revisão. Quando trabalha com a equipa de Suporte do Microsoft, podem solicitar que transmita a saída gerada pela PerfInsights para ajudar no processo de resolução de problemas.

A imagem que se segue mostra uma mensagem semelhante à que pode receber:

![Screenshot da mensagem da amostra do Microsoft Support](media/how-to-use-perfinsights-linux/support-email.png)

Siga as instruções na mensagem para aceder ao espaço de trabalho de transferência de ficheiros. Para obter segurança adicional, tem de alterar a sua palavra-passe na primeira utilização.

Depois de iniciar s-ss-fff.tar.gz ficheiro que foi recolhido pela **PerfInsights. \_ \_**
