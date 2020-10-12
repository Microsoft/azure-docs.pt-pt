---
title: StorSimple Virtual Array Update 0.2 & notas de lançamento 0.1
description: Descreve problemas e resoluções abertos críticos para o StorSimple Virtual Array que executa a Atualização 0.2 e 0.1.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80397868"
---
# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>StorSimple Virtual Array Update 0.2 e 0.1 notas de lançamento
## <a name="overview"></a>Descrição geral
As seguintes notas de lançamento identificam os problemas abertos críticos e os problemas resolvidos para as atualizações do Microsoft Azure StorSimple Virtual Array. (O Microsoft Azure StorSimple Virtual Array também é conhecido como o dispositivo virtual StorSimple no local ou o dispositivo virtual StorSimple.) 

As notas de lançamento são continuamente atualizadas, e à medida que são descobertas questões críticas que requerem uma solução alternativa, são adicionadas. Antes de implementar o seu dispositivo virtual StorSimple, reveja cuidadosamente as informações contidas nas notas de lançamento.

A atualização 0.2 corresponde à versão do software **10.0.10280.0**; A atualização 0.1 é a versão **10.0.10279.0**. As secções abaixo listam as alterações para cada atualização. 

> [!NOTE]
> As atualizações são disruptivas e reiniciarão o seu dispositivo. Se a I/O estiver em andamento, o dispositivo incorrerá em tempo de inatividade.
> 
> 

## <a name="issues-fixed-in-the-update-02"></a>Problemas corrigidos na Atualização 0.2
A atualização 0.2 inclui todas as alterações a partir da Atualização 0.1, para além da correção descrita no quadro seguinte:

| Funcionalidade | Problema |
| --- | --- |
| Atualizações |No último lançamento, as atualizações não foram detetadas automaticamente no portal clássico do Azure, pelo que teve de utilizar o UI web local para instalar atualizações. Esta questão é corrigida nesta versão. Depois de instalar o Update 0.2, pode instalar futuras atualizações utilizando o portal clássico Azure. |

## <a name="whats-new-in-the-update-01"></a>Novidades na Atualização 0.1
A atualização 0.1 contém as seguintes correções e melhorias de erros. 

* **Resiliência melhorada para interrupções na nuvem**: Este lançamento tem várias correções de bugs em torno da recuperação de desastres, backup, restauro e tiering em caso de perturbação da conectividade na nuvem. 
* **Melhor desempenho do restauro**: Esta versão tem correções de bugs que reduziram significativamente o tempo de conclusão dos trabalhos de restauro.
* **Otimização automatizada**da recuperação do espaço : Quando os dados são eliminados em volumes pouco aprovisionados, os blocos de armazenamento não reutilizados precisam de ser recuperados. Este lançamento melhorou o processo de recuperação do espaço a partir da nuvem, resultando no espaço não reutilizado a tornar-se mais rapidamente disponível em comparação com as versões anteriores.
* **Novas imagens de disco virtual**: Novos VHD, VHDX e VMDK estão agora disponíveis através do portal clássico Azure. Pode descarregar estas imagens para obter novos dispositivos Update 0.1.
* **Melhorar a exatidão do estado do emprego no portal**: Na versão anterior do software, o relatório do estado do trabalho no portal não era granular. Esta questão é resolvida nesta versão.
* **Experiência de adesão de domínios**: Correções de erros relacionadas com a junção de domínios e renomeação do dispositivo.

## <a name="issues-fixed-in-the-update-01"></a>Problemas corrigidos na Atualização 0.1
O quadro seguinte fornece um resumo das questões corrigidas nesta versão.

| N.º | Funcionalidade | Problema |
| --- | --- | --- |
| 1 |VMDK |Em algumas versões VMware, o disco DE foi visto como escasso causando alertas e interrompendo as operações normais. Isto foi corrigido neste comunicado. |
| 2 |servidor iSCSI |Na última versão, o utilizador foi obrigado a especificar um gateway para cada interface de rede ativada do seu dispositivo virtual StorSimple. Este comportamento é alterado nesta versão para que o utilizador tenha de configurar pelo menos um portal para todas as interfaces de rede ativadas. |
| 3 |Pacote de apoio |Na versão anterior do software, a recolha de pacotes de suporte falhou quando os tamanhos do pacote eram superiores a 1 GB. Esta questão é corrigida nesta versão. |
| 4 |Acesso à nuvem |No último lançamento, se o StorSimple Virtual Array não tivesse conectividade de rede e fosse reiniciado, a UI local teria problemas de conectividade. Este problema é corrigido nesta libertação. |
| 5 |Gráficos de monitorização |No lançamento anterior, na sequência de uma falha do dispositivo, os gráficos de utilização da capacidade da nuvem apresentaram valores incorretos no portal clássico do Azure. Isto é corrigido na versão atual. |

## <a name="known-issues-in-the-update-01"></a>Emissões conhecidas na Atualização 0.1
A tabela seguinte fornece um resumo de questões conhecidas para o StorSimple Virtual Array e inclui os problemas de lançamento anotados a partir das versões anteriores. **Os problemas lançados nesta versão são marcados com um asterisco. Quase todos os problemas desta lista foram levados a partir do lançamento da AG do StorSimple Virtual Array.**

| N.º | Funcionalidade | Problema | Soluções/comentários |
| --- | --- | --- | --- |
| **1.** |Atualizações |Os dispositivos virtuais criados na versão de pré-visualização não podem ser atualizados para uma versão suportada da Disponibilidade Geral. |Estes dispositivos virtuais devem ser falhados para a libertação de Disponibilidade Geral utilizando um fluxo de trabalho de recuperação de desastres (DR). |
| **2.** |Disco de dados provisionado |Uma vez que tenha fornecedo um disco de dados de um determinado tamanho especificado e criado o dispositivo virtual StorSimple correspondente, não deve expandir ou encolher o disco de dados. Tentar fazê-lo resultará na perda de todos os dados nos níveis locais do dispositivo. | |
| **3.** |Política de grupo |Quando um dispositivo é unido ao domínio, a aplicação de uma política de grupo pode afetar negativamente o funcionamento do dispositivo. |Certifique-se de que a sua matriz virtual está na sua própria unidade organizacional (OU) para o Ative Directory e que não são aplicados objetos de política de grupo (GPO). |
| **4.** |Local web UI |Se as funcionalidades de segurança reforçadas estiverem ativadas no Internet Explorer (IE ESC), algumas páginas locais de UI web, tais como Resolução de Problemas ou Manutenção, podem não funcionar corretamente. Os botões nestas páginas também podem não funcionar. |Desligue as funcionalidades de segurança melhoradas no Internet Explorer. |
| **5.** |Local web UI |Numa máquina virtual Hyper-V, as interfaces de rede na UI web são apresentadas como interfaces de 10 Gbps. |Este comportamento é um reflexo do Hiper-V. O Hiper-V mostra sempre 10 Gbps para adaptadores de rede virtuais. |
| **6.** |Volumes ou ações hierárquicos |O bloqueio de alcance byte para aplicações que trabalhem com os volumes tiered StorSimple não é suportado. Se o bloqueio de alcance byte estiver ativado, o tiering StorSimple não funcionará. |As medidas recomendadas incluem: <br></br>Desligue o bloqueio do alcance do byte na lógica da aplicação.<br></br>Opte por colocar dados para esta aplicação em volumes fixados localmente em oposição a volumes hierárquicos.<br></br>*Caveat*: Se estiver ativado o uso de volumes fixos locais e o bloqueio de alcance byte, esteja ciente de que o volume fixado localmente pode estar on-line mesmo antes da restauração estar completa. Nestes casos, se uma restauração está em curso, então deve esperar que a restauração esteja concluída. |
| **7.** |Ações hierárquis |Trabalhar com ficheiros grandes pode resultar numa saída lenta. |Ao trabalhar com ficheiros grandes, recomendamos que o maior ficheiro seja inferior a 3% do tamanho da ação. |
| **8.** |Capacidade utilizada para ações |Pode ver o consumo de ações na ausência de quaisquer dados sobre a parte. Isto porque a capacidade utilizada para ações inclui metadados. | |
| **9.** |Recuperação após desastre |Só é possível efetuar a recuperação de desastres de um servidor de ficheiros para o mesmo domínio que o do dispositivo de origem. A recuperação de desastres para um dispositivo alvo noutro domínio não é suportada nesta versão. |Isto será implementado num lançamento posterior. |
| **10.** |Azure PowerShell |Os dispositivos virtuais StorSimple não podem ser geridos através do Azure PowerShell nesta versão. |Toda a gestão dos dispositivos virtuais deve ser feita através do portal clássico Azure e da UI web local. |
| **11.** |Alteração da palavra-passe |A consola de dispositivos de matriz virtual apenas aceita a entrada no formato de teclado en-US. | |
| **12.** |CHAP |As credenciais CHAP uma vez criadas não podem ser removidas. Além disso, se modificar as credenciais CHAP, terá de desligar os volumes e, em seguida, ativá-los on-line para que a mudança produza efeitos. |Estes serão abordados numa libertação posterior. |
| **13.** |servidor iSCSI |O "armazenamento usado" exibido para um volume iSCSI pode ser diferente no serviço StorSimple Manager e no anfitrião iSCSI. |O anfitrião iSCSI tem a vista do sistema de ficheiros.<br></br>O dispositivo vê os blocos atribuídos quando o volume estava no tamanho máximo. |
| **14.** |Servidor de ficheiros* |Se um ficheiro numa pasta tiver um Fluxo de Dados Alternativo (ADS) associado a ele, o ADS não é apoiado ou restaurado através da recuperação de desastres, clone e recuperação do nível de item. | |

## <a name="next-step"></a>Passo seguinte
[Instale atualizações](storsimple-ova-install-update-01.md) no seu Conjunto Virtual StorSimple.

