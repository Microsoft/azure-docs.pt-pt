---
title: StorSimple Virtual Array Update 1.0 notas de lançamento
description: Descreve problemas e resoluções abertos críticos para o StorSimple Virtual Array que executa a Atualização 1.0.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 2ec88e4e97d45b27c0226198491b3adec6448496
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000900"
---
# <a name="storsimple-virtual-array-update-10-release-notes"></a>StorSimple Virtual Array Update 1.0 notas de lançamento

## <a name="overview"></a>Descrição geral

As seguintes notas de lançamento identificam os problemas abertos críticos e os problemas resolvidos para as atualizações do Microsoft Azure StorSimple Virtual Array.

As notas de lançamento são continuamente atualizadas, e à medida que são descobertas questões críticas que requerem uma solução alternativa, são adicionadas. Antes de implementar o seu StorSimple Virtual Array, reveja cuidadosamente as informações contidas nas notas de lançamento.

A atualização 1.0 corresponde à versão do software **10.0.10296.0**.

> [!IMPORTANT]
> - As atualizações são disruptivas e reiniciam o seu dispositivo. Se a I/O estiver em andamento, o dispositivo incorre em tempo de inatividade. Para obter instruções detalhadas sobre como aplicar a atualização, aceda à [Atualização de Instalação 1.0](storsimple-virtual-array-install-update-1.md).
>
> - A atualização 1 só está disponível para si através do portal Azure se o seu dispositivo estiver a executar a atualização 0.6.

## <a name="whats-new-in-update-10"></a>Novidades na Atualização 1.0

**A atualização 1.0 contém alterações relacionadas com a autenticação do serviço StorSimple Device Manager e deve ser implementada o mais cedo possível.** Esta atualização contém as seguintes melhorias e correções de erros:

 - **Utilização do Azure Ative Directory (AAD) para autenticar com o serviço StorSimple Device Manager** – A partir da Atualização 1.0 em diante, o Azure Ative Directory é utilizado para autenticar com o serviço StorSimple Device Manager. O antigo mecanismo de autenticação será depreciado até dezembro de 2017. Todos os utilizadores devem incluir os novos URLs de autenticação nas suas regras de firewall. Para mais informações, aceda aos URLs de autenticação listados nos [requisitos de Networking para o seu StorSimple Virtual Array](storsimple-ova-system-requirements.md).
 
    Se o URL de autenticação não estiver incluído nas regras de firewall, os utilizadores verão um alerta crítico de que o seu dispositivo StorSimple não poderia autenticar com o serviço. Se os utilizadores virem este alerta, têm de incluir o novo URL de autenticação. Para mais informações, aceda aos [alertas de networking StorSimple](storsimple-virtual-array-manage-alerts.md).

 - **Melhoria do desempenho** - Foram feitas várias correções de bugs para melhorar as velocidades das leituras de nuvens, tier-ins e tier-outs. Como resultado, tanto o desempenho de backup como restaurar melhorou para iSCSI e dispositivos de servidor de ficheiros.

 - **Melhoria da recolha de lixo** - Este lançamento tem correções de bugs que melhoram o desempenho do ciclo de recolha de lixo quando o dispositivo e a conta de armazenamento estão em duas regiões distantes.

 - **Melhoria do registo** - Esta versão contém melhorias no registo relacionado com a recolha de lixo e o caminho de I/O.


## <a name="issues-fixed-in-update-10"></a>Problemas fixados na Atualização 1.0

O quadro seguinte fornece um resumo das questões corrigidas nesta versão.

| N.º | Funcionalidade | Problema |
| --- | --- | --- |
| 1 |Autenticação baseada em AAD| Esta versão contém alterações que permitem à AAD autenticar com o Gestor de Dispositivos StorSimple.|
| 2 |Recolha de lixo| Este problema foi reportado num site de clientes onde o dispositivo e as contas de armazenamento se encontram em diferentes regiões e o cliente relatou erros de rede intermitentes com impacto na faturação. Neste comunicado, esta questão foi corrigida. |
| 3 |Desempenho| Esta versão contém alterações que resultam em leituras de restauração/nuvem/melhoria do desempenho de nível/nível.|
| 4 |Atualizar| Houve um problema com a atualização no lançamento anterior que resultou em falhas de backup num site do cliente. Esta questão é corrigida nesta versão.|

## <a name="known-issues-in-update-10"></a>Problemas conhecidos na Atualização 1.0

A tabela seguinte fornece um resumo de questões conhecidas para o StorSimple Virtual Array e inclui os problemas de lançamento anotados a partir das versões anteriores.

| N.º | Funcionalidade | Problema | Soluções/comentários |
| --- | --- | --- | --- |
| **1.** |Atualizações |As matrizes virtuais criadas na versão de pré-visualização não podem ser atualizadas para uma versão suportada da Disponibilidade Geral. |Estes conjuntos virtuais devem ser falhados para a libertação de Disponibilidade Geral utilizando um fluxo de trabalho de recuperação de desastres (DR). |
| **2.** |Disco de dados provisionado |Uma vez que tenha fornecedo um disco de dados de um determinado tamanho especificado e criado o conjunto virtual StorSimple correspondente, não deve expandir ou encolher o disco de dados. Tentar fazer resulta numa perda de todos os dados nos níveis locais do dispositivo. | |
| **3.** |Política de grupo |Quando um dispositivo é unido ao domínio, a aplicação de uma política de grupo pode afetar negativamente o funcionamento do dispositivo. |Certifique-se de que a sua matriz virtual está na sua própria unidade organizacional (OU) para o Ative Directory e que não são aplicados objetos de política de grupo (GPO). |
| **4.** |Local web UI |Se as funcionalidades de segurança reforçadas estiverem ativadas no Internet Explorer (IE ESC), algumas páginas locais de UI web, tais como Resolução de Problemas ou Manutenção, podem não funcionar corretamente. Os botões nestas páginas também podem não funcionar. |Desligue as funcionalidades de segurança melhoradas no Internet Explorer. |
| **5.** |Local web UI |Numa máquina virtual Hyper-V, as interfaces de rede na UI web são apresentadas como interfaces de 10 Gbps. |Este comportamento é um reflexo do Hiper-V. O Hiper-V mostra sempre 10 Gbps para adaptadores de rede virtuais. |
| **6.** |Volumes ou ações hierárquicos |O bloqueio de alcance byte para aplicações que trabalhem com os volumes tiered StorSimple não é suportado. Se o bloqueio de alcance byte estiver ativado, o tiering StorSimple não funciona. |As medidas recomendadas incluem: <br></br>Desligue o bloqueio do alcance do byte na lógica da aplicação.<br></br>Opte por colocar dados para esta aplicação em volumes fixados localmente em oposição a volumes hierárquicos.<br></br>*Caveat*: Quando se utilizam volumes fixados localmente e o bloqueio de alcance byte está ativado, o volume fixado localmente pode estar on-line mesmo antes da restauração estar completa. Nestes casos, se uma restauração está em curso, então deve esperar que a restauração esteja concluída. |
| **7.** |Ações hierárquis |Trabalhar com ficheiros grandes pode resultar numa saída lenta. |Ao trabalhar com ficheiros grandes, recomendamos que o maior ficheiro seja inferior a 3% do tamanho da ação. |
| **8.** |Capacidade utilizada para ações |Pode ver o consumo de ações quando não há dados sobre a parte. Este consumo deve-se ao facto de a capacidade utilizada para as ações incluir metadados. | |
| **9.** |Recuperação após desastre |Só é possível efetuar a recuperação de desastres de um servidor de ficheiros para o mesmo domínio que o do dispositivo de origem. A recuperação de desastres para um dispositivo alvo noutro domínio não é suportada nesta versão. |Isto é implementado num lançamento posterior. Para mais informações, vá ao [Failover e à recuperação de desastres para o seu StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |As Matrizes Virtuais StorSimple não podem ser geridas através do Azure PowerShell nesta versão. |Toda a gestão dos dispositivos virtuais deve ser feita através do portal Azure e da UI web local. |
| **11.** |Alteração da palavra-passe |A consola de dispositivos de matriz virtual apenas aceita a entrada no formato de teclado en-us. | |
| **12.** |CHAP |As credenciais CHAP uma vez criadas não podem ser removidas. Além disso, se modificar as credenciais CHAP, precisa de desligar os volumes e, em seguida, trazê-los on-line para que a mudança produza efeitos. |Esta questão é abordada numa versão posterior. |
| **13.** |servidor iSCSI |O "armazenamento usado" exibido para um volume iSCSI pode ser diferente no serviço StorSimple Device Manager e no anfitrião iSCSI. |O anfitrião iSCSI tem a vista do sistema de ficheiros.<br></br>O dispositivo vê os blocos atribuídos quando o volume estava no tamanho máximo. |
| **14.** |Servidor de ficheiros |Se um ficheiro numa pasta tiver um Fluxo de Dados Alternativo (ADS) associado a ele, o ADS não é apoiado ou restaurado através da recuperação de desastres, clone e recuperação do nível de item. | |
| **15.** |Servidor de ficheiros |As ligações simbólicas não são suportadas. | |
| **16.** |Servidor de ficheiros |Os ficheiros protegidos pelo Sistema de Ficheiros encriptadores do Windows (EFS) quando copiados ou armazenados no servidor de ficheiros StorSimple Virtual Array resultam numa configuração não suportada.  | |
| **17.** |Atualizações |Se vir código de erro: 2359302 (hex 0x240006) ao tentar instalar um hotfix através da UI local, então isto implica que o hotfix já está instalado no seu dispositivo.   | |
| **18.** |Atualizações |Se utilizar o UI web local para instalar o Update 1 na sua matriz virtual, tem de se certificar de que está a executar a Atualização 0.6. Se estiver a executar uma versão inferior à atualização 0.6, tem de instalar o Update 0.6 primeiro e depois aplicar o Update 1. Se instalar diretamente o Update 1.0 a partir de uma versão pré-Actualização 0.6, perderá algumas atualizações e os gráficos de monitorização não funcionarão.   | |


## <a name="next-steps"></a>Passos seguintes
[Instale a Atualização 1.0](storsimple-virtual-array-install-update-1.md) no seu StorSimple Virtual Array.

## <a name="references"></a>Referências
À procura de uma nota de lançamento mais antiga? Vá para:
*  [StorSimple Virtual Array Update 0.6 Notas de lançamento](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0.5 Notas de lançamento](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0.4 Notas de lançamento](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 Notas de lançamento](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 e 0.2 Notas de lançamento](storsimple-ova-update-01-release-notes.md)
* [StorSimple Notas de lançamento de disponibilidade geral de matriz virtual](./storsimple-virtual-array-update-06-release-notes.md)