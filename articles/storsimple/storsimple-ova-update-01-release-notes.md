---
title: StorSimple Virtual Array Update 0.2 & 0.1 notas de lançamento
description: Descreve questões e resoluções críticas abertas para o StorSimple Virtual Array executando Update 0.2 e 0.1.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 3993864d-2ddd-4302-a2f1-8d737fba6eab
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/16/2016
ms.author: alkohli
ms.openlocfilehash: 95386f36340aca470769c920e40bbb70e09d34fc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80397868"
---
# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>StorSimple Virtual Array Update 0.2 e 0.1 notas de lançamento
## <a name="overview"></a>Descrição geral
As seguintes notas de lançamento identificam os problemas abertos críticos e os problemas resolvidos para as atualizações do Microsoft Azure StorSimple Virtual Array. (Microsoft Azure StorSimple Virtual Array também é conhecido como o dispositivo virtual StorSimple on-premido ou o dispositivo virtual StorSimple.) 

As notas de lançamento são continuamente atualizadas e, à medida que são descobertas questões críticas que exigem uma suver, são adicionadas. Antes de implementar o seu dispositivo virtual StorSimple, reveja cuidadosamente as informações contidas nas notas de lançamento.

A atualização 0.2 corresponde à versão do software **10.0.10280.0;** A atualização 0.1 é a versão **10.0.10279.0**. As secções abaixo listam as alterações para cada atualização. 

> [!NOTE]
> As atualizações são disruptivas e reiniciam o seu dispositivo. Se o I/O estiver em curso, o dispositivo incorrerá em tempo de inatividade.
> 
> 

## <a name="issues-fixed-in-the-update-02"></a>Problemas corrigidos na Atualização 0.2
A atualização 0.2 inclui todas as alterações a partir da Atualização 0.1 para além da correção descrita no quadro seguinte:

| Funcionalidade | Problema |
| --- | --- |
| Atualizações |No último lançamento, as atualizações não foram detetadas automaticamente no portal clássico do Azure, pelo que teve de utilizar a Web UI local para instalar atualizações. Esta questão está corrigida nesta versão. Depois de instalar o Update 0.2, pode instalar futuras atualizações utilizando o portal clássico Azure. |

## <a name="whats-new-in-the-update-01"></a>Novidades na Atualização 0.1
A atualização 0.1 contém as seguintes correções e melhorias do bug. 

* **Melhor resiliência para interrupções na nuvem**: Esta libertação tem várias correções de bugs em torno da recuperação de desastres, backup, restauro e tiering em caso de interrupção da conectividade da nuvem. 
* **Melhor desempenho**de restauro : Esta libertação tem correções de bugs que reduziram significativamente o tempo de conclusão dos trabalhos de restauro.
* **Otimização automatizada da recuperação**do espaço : Quando os dados são eliminados em volumes pouco provisionados, os blocos de armazenamento não utilizados precisam de ser recuperados. Esta versão melhorou o processo de recuperação do espaço a partir da nuvem, resultando em que o espaço não utilizado se tornasse mais rápido em comparação com as versões anteriores.
* **Novas imagens de disco virtual**: New VHD, VHDX e VMDK estão agora disponíveis através do portal clássico Azure. Pode descarregar estas imagens para fornecer novos dispositivos Update 0.1.
* **Melhorar a precisão do estado do emprego no portal**: Na versão anterior do software, a comunicação do estado de trabalho no portal não era granular. Esta questão está resolvida nesta libertação.
* **Experiência de adesão**ao domínio : Correções de bugs relacionadas com a junção de domínio e renomeação do dispositivo.

## <a name="issues-fixed-in-the-update-01"></a>Problemas corrigidos na Atualização 0.1
A tabela que se segue apresenta um resumo das questões fixadas nesta versão.

| Não. | Funcionalidade | Problema |
| --- | --- | --- |
| 1 |VMDK |Em algumas versões VMware, o disco OS foi visto como escasso causando alertas e interrompendo as operações normais. Isto foi corrigido nesta libertação. |
| 2 |servidor iSCSI |No último lançamento, o utilizador foi obrigado a especificar um portal para cada interface de rede ativada do seu dispositivo virtual StorSimple. Este comportamento é alterado nesta versão para que o utilizador tenha de configurar pelo menos uma porta de entrada para todas as interfaces de rede ativadas. |
| 3 |Pacote de apoio |Na versão anterior do software, a recolha de pacotes de suporte falhou quando os tamanhos do pacote eram superiores a 1 GB. Esta questão está corrigida nesta versão. |
| 4 |Acesso em nuvem |No último lançamento, se o StorSimple Virtual Array não tivesse conectividade de rede e fosse reiniciado, a UI local teria problemas de conectividade. Este problema está resolvido nesta libertação. |
| 5 |Gráficos de monitorização |No lançamento anterior, após uma falha no dispositivo, os gráficos de utilização da capacidade da nuvem apresentavam valores incorretos no portal clássico azure. Isto está fixado na versão atual. |

## <a name="known-issues-in-the-update-01"></a>Questões conhecidas na Atualização 0.1
A tabela seguinte fornece um resumo de questões conhecidas para o StorSimple Virtual Array e inclui os problemas de lançamento anotados a partir dos lançamentos anteriores. **As questões divulgadas nesta versão são marcadas com um asterisco. Quase todos os problemas desta lista foram levados para a versão ga da StorSimple Virtual Array.**

| Não. | Funcionalidade | Problema | Seleção/comentários |
| --- | --- | --- | --- |
| **1.** |Atualizações |Os dispositivos virtuais criados no lançamento de pré-visualização não podem ser atualizados para uma versão de Disponibilidade Geral suportada. |Estes dispositivos virtuais devem ser falhados para a libertação de Disponibilidade Geral utilizando um fluxo de trabalho de recuperação de desastres (DR). |
| **2.** |Disco de dados provisionado |Depois de ter aprovisionado um disco de dados de um determinado tamanho especificado e ter criado o dispositivo virtual StorSimple correspondente, não deve expandir ou encolher o disco de dados. Tentar fazê-lo resultará na perda de todos os dados nos níveis locais do dispositivo. | |
| **3.** |Política de grupo |Quando um dispositivo é unido ao domínio, aplicar uma política de grupo pode afetar negativamente o funcionamento do dispositivo. |Certifique-se de que a sua matriz virtual está na sua própria unidade organizacional (OU) para o Ative Directory e não são aplicados objetos de política de grupo (GPO). |
| **4.** |Local web UI |Se as funcionalidades de segurança melhoradas forem ativadas no Internet Explorer (IE ESC), algumas páginas de UI web locais, como a Resolução de Problemas ou Manutenção, podem não funcionar corretamente. Os botões nestas páginas também podem não funcionar. |Desligue as funcionalidades de segurança melhoradas no Internet Explorer. |
| **5.** |Local web UI |Numa máquina virtual Hyper-V, as interfaces de rede na UI web são exibidas como interfaces de 10 Gbps. |Este comportamento é um reflexo do Hyper-V. O Hyper-V mostra sempre 10 Gbps para adaptadores de rede virtuais. |
| **6.** |Volumes ou ações hierárquicos |Não é suportado o bloqueio de gama byte para aplicações que funcionem com os volumes storSimple tiered. Se o bloqueio do alcance byte estiver ativado, o diferencial StorSimple não funcionará. |As medidas recomendadas incluem: <br></br>Desligue o alcance do byte na sua lógica de aplicação.<br></br>Opte por colocar dados para esta aplicação em volumes fixados localmente em oposição aos volumes hierárquicos.<br></br>*Caveat*: Se estiver ativado o uso de volumes fixados localmente e o bloqueio de alcance byte, tenha em atenção que o volume fixado localmente pode estar on-line mesmo antes de a restauração estar concluída. Nesses casos, se houver um restauro em curso, então deve esperar que o restauro esteja concluído. |
| **7.** |Ações hierárquias |Trabalhar com ficheiros grandes pode resultar numa saída lenta. |Ao trabalhar com ficheiros grandes, recomendamos que o maior ficheiro seja inferior a 3% do tamanho da ação. |
| **8.** |Capacidade utilizada para ações |Pode ver o consumo de ações na ausência de dados sobre a parte. Isto porque a capacidade utilizada para ações inclui metadados. | |
| **9.** |Recuperação após desastre |Só é possível realizar a recuperação de desastres de um servidor de ficheiros para o mesmo domínio que o do dispositivo de origem. A recuperação de desastres para um dispositivo-alvo noutro domínio não é suportada nesta versão. |Isto será implementado numa versão posterior. |
| **10.** |Azure PowerShell |Os dispositivos virtuais StorSimple não podem ser geridos através do Azure PowerShell nesta versão. |Toda a gestão dos dispositivos virtuais deve ser feita através do portal clássico Azure e da UI web local. |
| **11.** |Alteração da palavra-passe |A consola de dispositivos de matriz virtual só aceita entrada em formato de teclado en-EUA. | |
| **12.** |CHAP |As credenciais CHAP uma vez criadas não podem ser removidas. Além disso, se modificar as credenciais CHAP, terá de desligar os volumes e, em seguida, trazê-los online para que a mudança entre em vigor. |Estes serão abordados numa versão posterior. |
| **13.** |servidor iSCSI |O 'armazenamento usado' apresentado para um volume iSCSI pode ser diferente no serviço StorSimple Manager e no anfitrião iSCSI. |O anfitrião do iSCSI tem a visão do sistema de ficheiros.<br></br>O dispositivo vê os blocos atribuídos quando o volume estava no tamanho máximo. |
| **14.** |Servidor de ficheiros* |Se um ficheiro numa pasta tiver um Fluxo de Dados Alternativo (ADS) associado a ele, o ADS não é apoiado ou restaurado através da recuperação de desastres, clone e recuperação do nível do item. | |

## <a name="next-step"></a>Passo seguinte
[Instale atualizações](storsimple-ova-install-update-01.md) no seu StorSimple Virtual Array.

