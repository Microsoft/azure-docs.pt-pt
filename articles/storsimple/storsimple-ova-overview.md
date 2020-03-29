---
title: Visão geral da Microsoft Azure StorSimple Virtual Array
description: Descreve o StorSimple Virtual Array, uma solução de armazenamento integrada que gere tarefas de armazenamento entre uma matriz virtual no local e armazenamento em nuvem microsoft Azure.
author: alkohli
ms.assetid: 169c639b-1124-46a5-ae69-ba9695525b77
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 32781a83aec996b23f161f5fe695f39a0de38685
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273876"
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>Introdução ao StorSimple Virtual Array

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Descrição geral

O Microsoft Azure StorSimple Virtual Array é uma solução de armazenamento integrada que gere tarefas de armazenamento entre uma matriz virtual no local que funciona num hipervisor e armazenamento em nuvem microsoft Azure. A matriz virtual é um servidor de ficheiros eficiente, rentável e facilmente gerido ou uma solução de servidor iSCSI que elimina muitos dos problemas e despesas associados ao armazenamento da empresa e proteção de dados. A matriz virtual é particularmente adequada para o armazenamento de dados de arquivo pouco frequentemente acedidos.

Este artigo fornece uma visão geral da matriz virtual - aqui estão alguns outros recursos:

* Para obter as melhores práticas, consulte as [melhores práticas da StorSimple Virtual Array](storsimple-ova-best-practices.md).
* Para uma visão geral dos dispositivos da série StorSimple 8000, vá à [série StorSimple 8000: uma solução híbrida em nuvem](storsimple-overview.md).
* Para obter informações sobre os dispositivos da série StorSimple 5000/7000, vá à [StorSimple Online Help](http://onlinehelp.storsimple.com/).

A matriz virtual suporta o protocolo iSCSI ou Server Message Block (SMB). Funciona na sua infraestrutura hipervisor existente e fornece tiering para a nuvem, backup de nuvem, restauro rápido, recuperação de nível de item e características de recuperação de desastres.

A tabela que se segue resume as características importantes do StorSimple Virtual Array.

| Funcionalidade | Matriz Virtual do StorSimple |
| --- | --- |
| Requisitos de instalação |Utiliza infraestrutura de virtualização (Hiper-V ou VMware) |
| Disponibilidade |Nó único |
| Capacidade total (incluindo nuvem) |Capacidade utilizável até 64 TB por matriz virtual |
| Capacidade local |390 GB a 6.4 TB capacidade utilizável por matriz virtual (necessidade de fornecer 500 GB a 8 TB de espaço em disco) |
| Protocolos nativos |iSCSI ou SMB |
| Objetivo de tempo de recuperação (RTO) |iSCSI: menos de 2 minutos, independentemente do tamanho |
| Objetivo de ponto de recuperação (RPO) |Backups diários e backups a pedido |
| Tiering de armazenamento |Usa mapeamento de calor para determinar que dados devem ser nividados dentro ou fora |
| Suporte |Infraestrutura de virtualização apoiada pelo fornecedor |
| Desempenho |Varia dependendo das infraestruturas subjacentes |
| Mobilidade de dados |Pode restaurar para o mesmo dispositivo ou fazer a recuperação ao nível do item (servidor de ficheiros) |
| Camadas de armazenamento |Armazenamento e nuvem de hipervisor local |
| Tamanho da partilha |Tiered: até 20 TB; fixada localmente: até 2 TB |
| Tamanho do volume |Tiered: 500 GB a 5 TB; fixada localmente: 50 GB a 200 GB <br> A reserva local máxima para volumes hierárquicos é de 200 GB. |
| Instantâneos |Consistente com a falha |
| Recuperação ao nível do item |Sim, o que é que se está utilizadores podem restaurar a partir de ações |

## <a name="why-use-storsimple"></a>Porquê usar o StorSimple?

O StorSimple liga utilizadores e servidores ao armazenamento Do Azure em minutos, sem modificação de aplicação.

A tabela seguinte descreve alguns dos principais benefícios que a solução StorSimple Virtual Array proporciona.

| Funcionalidade | Vantagem |
| --- | --- |
| Integração transparente |A matriz virtual suporta o iSCSI ou o protocolo SMB. O movimento de dados entre o nível local e o nível de nuvem é perfeito e transparente para o utilizador. |
| Custos reduzidos de armazenamento |Com o StorSimple, você disponibiliza armazenamento local suficiente para atender às exigências atuais dos dados quentes mais utilizados. À medida que as necessidades de armazenamento crescem, a StorSimple nivete dados frios em armazenamento de nuvem rentável. Os dados são desduplicados e comprimidos antes de enviar para a nuvem para reduzir ainda mais os requisitos de armazenamento e despesas. |
| Gestão simplificada de armazenamento |O StorSimple fornece uma gestão centralizada na nuvem utilizando o StorSimple Device Manager para gerir vários dispositivos. |
| Melhor recuperação e conformidade de desastres |O StorSimple facilita a recuperação mais rápida do desastre, restaurando imediatamente os metadados e restaurando os dados conforme necessário. Isto significa que as operações normais podem continuar com o mínimo de perturbação. |
| Mobilidade de dados |Os dados hierarquizados para a nuvem podem ser acedidos a partir de outros locais para fins de recuperação e migração. Note que pode restaurar os dados apenas para a matriz virtual original. No entanto, você usa funcionalidades de recuperação de desastres para restaurar toda a matriz virtual para outra matriz virtual. |

## <a name="storsimple-workload-summary"></a>Resumo da carga de trabalho StorSimple

Um resumo das cargas de trabalho storSimple suportadas é tabulado abaixo.

|Cenário     |Carga de trabalho     |Suportado      |Restrições               | Versões aplicáveis|
|-------------|-------------|---------------|---------------------------|--------------------|
|Escritório Remoto/Sucursal (ROBO)  |Partilha de ficheiros     |Sim      |Consulte [os limites máximos para o servidor](storsimple-ova-limits.md)de ficheiros .<br></br>Consulte [os requisitos do sistema para versões SMB suportadas](storsimple-ova-system-requirements.md).| Todas as versões     |
|Arquivo de nuvens  |Partilha de ficheiros de arquivo     |Sim      |Consulte [os limites máximos para o servidor](storsimple-ova-limits.md)de ficheiros .<br></br>Consulte [os requisitos do sistema para versões SMB suportadas](storsimple-ova-system-requirements.md).| Todas as versões     |

O StorSimple Virtual Array é mais adequado para dados pouco frequentemente acedidos. Embora a matriz virtual tenha uma cache local para aumentar o desempenho, os utilizadores devem assumir que os ficheiros de serviços do dispositivo no nível mais baixo de armazenamento (a nuvem). Cada matriz virtual pode escrever e ler para o armazenamento Azure a aproximadamente 100 Mbps. Essa ligação é partilhada em todos os pedidos que entram no dispositivo e pode tornar-se um estrangulamento, como mostra o diagrama abaixo.

![Arquivo de nuvens](./media/storsimple-ova-overview/cloud-archiving.png)

Quando vários utilizadores simultâneos acedem à matriz virtual, todos partilham a ligação ao Azure, levando a um desempenho mais baixo. Não existe um desempenho garantido por utilizador e o dispositivo processa pedidos individuais à medida que chegam.

StorSimple Virtual Array não é adequado para cargas de trabalho que requerem alta disponibilidade. A matriz virtual é um único dispositivo de nó que experimenta o tempo de inatividade quando as atualizações de software são instaladas. Os administradores devem planear uma janela de manutenção de 30 minutos 3-4 vezes por ano.

## <a name="workflows"></a>Fluxos de trabalho

O StorSimple Virtual Array é particularmente adequado para os seguintes fluxos de trabalho:

* [Gestão de armazenamento baseada em nuvem](#cloud-based-storage-management)
* [Backup independente de localização](#location-independent-backup)
* [Proteção de dados e recuperação de desastres](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Gestão de armazenamento baseada em nuvem
Pode utilizar o serviço StorSimple Device Manager a funcionar no portal Azure para gerir os dados armazenados em vários dispositivos e em vários locais. Isto é particularmente útil em cenários de ramificação distribuídos. Note que deve criar instâncias separadas do serviço StorSimple Device Manager para gerir matrizes virtuais e dispositivos físicos StorSimple. Note também que a matriz virtual agora utiliza o novo portal Azure em vez do portal clássico Azure.

![gestão de armazenamento baseada em nuvem](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Backup independente de localização
Com a matriz virtual, as imagens em nuvem fornecem uma cópia independente de localização, ponto-a-tempo de um volume ou partilha. Os instantâneos em nuvem são ativados por padrão e não podem ser desativados. Todos os volumes e ações são backup ao mesmo tempo através de uma única política diária de backup, e você pode obter backups ad hoc adicionais sempre que necessário.

### <a name="data-protection-and-disaster-recovery"></a>Proteção de dados e recuperação de desastres
O matriz virtual suporta os seguintes cenários de proteção de dados e de recuperação de desastres:

* **Volume ou restauro de partilha** – Utilize o restauro como novo fluxo de trabalho para recuperar um volume ou partilha. Utilize esta abordagem para recuperar todo o volume ou partilha.
* **Recuperação ao nível do item** – As ações permitem um acesso simplificado a cópias de segurança recentes. Pode recuperar facilmente um ficheiro individual de uma pasta especial *.backup* disponível na nuvem. Esta capacidade de restauro é orientada pelo utilizador e não é necessária nenhuma intervenção administrativa.
* **Recuperação de desastres** – Utilize a capacidade de failover para recuperar todos os volumes ou partilhas para uma nova matriz virtual. Cria a nova matriz virtual e regista-a com o serviço StorSimple Device Manager e falha-se na matriz virtual original. A nova matriz virtual assumirá então os recursos provisionados.

## <a name="storsimple-virtual-array-components"></a>Componentes storSimple Virtual Array

A matriz virtual inclui os seguintes componentes:

* [Matriz virtual](#virtual-array) – Um dispositivo híbrido de armazenamento em nuvem baseado numa máquina virtual aprovisionada no seu ambiente virtualizado ou hipervisor.
* [Serviço StorSimple Device Manager](#storsimple-device-manager-service) – Uma extensão do portal Azure que permite gerir um ou mais dispositivos StorSimple a partir de uma única interface web a que pode aceder a partir de diferentes localizações geográficas. Pode utilizar o serviço StorSimple Device Manager para criar e gerir serviços, visualizar e gerir dispositivos e alertas, e gerir volumes, partilhas e instantâneos existentes.
* [Interface de utilizador web local](#local-web-user-interface) – Um UI baseado na Web que é usado para configurar o dispositivo para que possa ligar-se à rede local e, em seguida, registar o dispositivo com o serviço StorSimple Device Manager. 
* [Interface de linha de comando](#command-line-interface) – Interface Windows PowerShell que pode utilizar para iniciar uma sessão de suporte na matriz virtual.
  As seguintes secções descrevem cada um destes componentes com maior detalhe e explicam como a solução organiza dados, aloca armazenamento e facilita a gestão de armazenamento e proteção de dados.

### <a name="virtual-array"></a>Matriz virtual

A matriz virtual é uma solução de armazenamento de nó único que fornece armazenamento primário, gere a comunicação com armazenamento em nuvem, e ajuda a garantir a segurança e confidencialidade de todos os dados que são armazenados no dispositivo.

A matriz virtual está disponível num modelo que está disponível para download. A matriz virtual tem uma capacidade máxima de 6,4 TB no dispositivo (com um requisito de armazenamento subjacente de 8 TB) e 64 TB, incluindo armazenamento em nuvem.

A matriz virtual tem as seguintes características:

* É rentável. Faz uso da sua infraestrutura de virtualização existente e pode ser implantado no seu hipervisor Hiper-V ou VMware existente.
* Reside no datacenter e pode ser configurado como um servidor iSCSI ou um servidor de ficheiros.
* Está integrado com a nuvem.
* As cópias de segurança são armazenadas na nuvem, o que pode facilitar a recuperação de desastres e simplificar a recuperação do nível do item (ILR).
* Pode aplicar atualizações à matriz virtual, tal como as aplicaria a um dispositivo físico.

> [!NOTE]
> Uma matriz virtual não pode ser expandida. Portanto, é importante fornecer armazenamento adequado quando você cria a matriz virtual.

### <a name="storsimple-device-manager-service"></a>Serviço de Gestor de Dispositivos do StorSimple

O Microsoft Azure StorSimple fornece uma interface de utilizador baseada na Web, o serviço StorSimple Device Manager, que lhe permite gerir centralmente o armazenamento StorSimple. Pode utilizar o serviço StorSimple Device Manager para executar as seguintes tarefas:

* Gerencie várias Matrizes Virtuais StorSimple a partir de um único serviço.
* Configure e gerencie as definições de segurança para StorSimple Virtual Arrays. (A encriptação na nuvem depende de APIs do Microsoft Azure.)
* Configure credenciais e propriedades da conta de armazenamento.
* Configure e gere volumes ou partilhas.
* Recue e restaure os dados em volumes ou partilhas.
* Monitor desempenho.
* Reveja as definições do sistema e identifique possíveis problemas.

Utiliza o serviço StorSimple Device Manager para realizar a administração diária da sua matriz virtual.

Para mais informações, vá ao [serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-virtual-array-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Interface de utilizador web local

A matriz virtual inclui um UI baseado na Web que é usado para configuração e registo único do dispositivo com o serviço StorSimple Device Manager. Pode usá-lo para desligar e reiniciar a matriz virtual, executar testes de diagnóstico, atualizar software, alterar a palavra-passe do administrador do dispositivo, ver registos do sistema e contactar o Microsoft Support para apresentar um pedido de serviço.

Para obter informações sobre a utilização do UI baseado na web, vá utilizar [o UI baseado na web para administrar o seu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Interface de linha de comando

A interface Windows PowerShell incluída permite iniciar uma sessão de suporte com o Microsoft Support para que possam ajudá-lo a resolver problemas e resolver problemas que possa encontrar na sua matriz virtual.

## <a name="storage-management-technologies"></a>Tecnologias de gestão de armazenamento

Além da matriz virtual e outros componentes, a solução StorSimple utiliza as seguintes tecnologias de software para fornecer acesso rápido a dados importantes, reduzir o consumo de armazenamento e proteger os dados armazenados na sua matriz virtual:

* [Tiering automático de armazenamento](#automatic-storage-tiering) 
* [Ações e volumes locais fixados](#locally-pinned-shares-and-volumes)
* Desduplicação e compressão para dados niferidos ou apoiados até à nuvem 
* [Backups programados e a pedido](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Tiering automático de armazenamento
A matriz virtual usa um novo mecanismo de tiering para gerir os dados armazenados através da matriz virtual e da nuvem. Existem apenas dois níveis: a matriz virtual local e o armazenamento em nuvem Azure. O StorSimple Virtual Array organiza automaticamente dados para os níveis com base num mapa de calor, que rastreia o uso atual, idade e relações com outros dados. Os dados mais ativos (mais quentes) são armazenados localmente, enquanto os dados menos ativos e inativos são automaticamente migrados para a nuvem. (Todas as cópias de segurança estão armazenadas na nuvem.) O StorSimple ajusta e reorganiza as atribuições de dados e armazenamento à medida que os padrões de utilização mudam. Por exemplo, algumas informações podem tornar-se menos ativas ao longo do tempo. À medida que se torna progressivamente menos ativa, é camadapara a nuvem. Se esses mesmos dados voltarem a estar ativos, este será nigred para o conjunto de armazenamento.

Os dados relativos a uma determinada quota ou volume tiered são garantidos o seu próprio espaço de nível local (aproximadamente 10% do espaço total previsto para essa partilha ou volume). Embora isso reduza o armazenamento disponível na matriz virtual para essa partilha ou volume, garante que o tiering para uma ação ou volume não será afetado pelas necessidades de tiering de outras ações ou volumes. Assim, uma carga de trabalho muito ocupada em uma parte ou volume não pode forçar todas as outras cargas de trabalho para a nuvem.

Os volumes hierárquicos criados para iSCSI têm uma reserva local máxima de 200 GB, independentemente do tamanho do volume.

![tiering de armazenamento automático](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> Pode especificar um volume como fixado localmente, caso em que os dados permanecem na matriz virtual e nunca são nividamente para a nuvem. Para mais informações, vá a [ações e volumes locais.](#locally-pinned-shares-and-volumes)


### <a name="locally-pinned-shares-and-volumes"></a>Ações e volumes locais fixados

Pode criar ações e volumes apropriados como fixado localmente. Esta capacidade garante que os dados exigidos por aplicações críticas permanecem na matriz virtual e nunca são nividamente para a nuvem. As ações e volumes fixados localmente têm as seguintes características:

* Não estão sujeitos a tardios em nuvem ou problemas de conectividade.
* Eles ainda beneficiam de storSimple cloud backup e recursos de recuperação de desastres.

Pode restaurar uma parte ou volume fixado localmente como tiered ou uma parte ou volume hierárquico sem identificação local. 

Para obter mais informações sobre volumes fixados localmente, vá utilizar [o serviço StorSimple Device Manager para gerir volumes](storsimple-virtual-array-manage-volumes.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Desduplicação e compressão para dados niferidos ou apoiados até à nuvem

O StorSimple utiliza a desduplicação e a compressão de dados para reduzir ainda mais os requisitos de armazenamento na nuvem. A duplicação reduz a quantidade global de dados armazenados eliminando o despedimento no conjunto de dados armazenado. À medida que a informação muda, o StorSimple ignora os dados inalterados e captura apenas as alterações. Além disso, o StorSimple reduz a quantidade de dados armazenados identificando e removendo informações duplicadas.

> [!NOTE]
> Os dados armazenados na matriz virtual não são desduplicados ou comprimidos. Toda a desduplicação e compressão ocorre pouco antes dos dados ser enviados para a nuvem.

### <a name="scheduled-and-on-demand-backups"></a>Backups programados e a pedido

As funcionalidades de proteção de dados StorSimple permitem criar cópias de segurança a pedido. Além disso, um calendário de cópia de segurança predefinido garante que os dados são apoiados diariamente. As cópias de segurança são tomadas sob a forma de instantâneos incrementais, que são armazenados na nuvem. Os instantâneos, que registam apenas as alterações desde a última cópia de segurança, podem ser criados e restaurados rapidamente. Estes instantâneos podem ser criticamente importantes em cenários de recuperação de desastres porque substituem sistemas de armazenamento secundário (como cópia de segurança da fita), e permitem restaurar os dados no seu datacenter ou para sites alternativos, se necessário.

## <a name="managing-personal-information"></a>Gestão de informações pessoais

O StorSimple Device Manager para séries virtuais recolhe informações pessoais em duas instâncias-chave:
 - Alerte as definições do utilizador sempre que os endereços de e-mail dos utilizadores estejam configurados. Esta informação pode ser apurada pelo administrador. 
 - Utilizadores que possam aceder aos dados residentes nas ações. Uma lista de utilizadores que podem aceder aos dados da partilha é apresentada e pode ser exportada. Esta lista também é eliminada quando as ações são eliminadas.

Para mais informações, reveja a política de privacidade da [Microsoft no Trust Center](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Passos seguintes

Aprenda a [preparar o portal de matriz virtual.](storsimple-virtual-array-deploy1-portal-prep.md)
