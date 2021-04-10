---
title: Visão geral do Microsoft Azure StorSimple De array virtual
description: Descreve o StorSimple Virtual Array, uma solução de armazenamento integrada que gere tarefas de armazenamento entre uma matriz virtual no local e o armazenamento em nuvem do Microsoft Azure.
author: alkohli
ms.assetid: 169c639b-1124-46a5-ae69-ba9695525b77
ms.service: storsimple
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 6d7d8c37d0e7ffacb3221a4f30a96ef6cccda819
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674449"
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>Introdução ao Array Virtual StorSimple

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Descrição Geral

O Microsoft Azure StorSimple Virtual Array é uma solução de armazenamento integrada que gere tarefas de armazenamento entre uma matriz virtual no local que funciona num hipervisor e armazenamento em nuvem Microsoft Azure. O conjunto virtual é uma solução eficiente, rentável e facilmente gerida por servidor de ficheiros ou servidor iSCSI que elimina muitos dos problemas e despesas associados ao armazenamento de empresa e proteção de dados. A matriz virtual é particularmente adequada para o armazenamento de dados de arquivo pouco acedidos.

Este artigo fornece uma visão geral da matriz virtual - aqui estão alguns outros recursos:

* Para as melhores práticas, consulte as [melhores práticas do Array Virtual StorSimple.](storsimple-ova-best-practices.md)
* Para uma visão geral dos dispositivos da série StorSimple 8000, vá à [série StorSimple 8000: uma solução de nuvem híbrida](storsimple-overview.md).
* Para obter informações sobre os dispositivos da série StorSimple 5000/7000, aceda à [Ajuda Online StorSimple](http://onlinehelp.storsimple.com/).

O conjunto virtual suporta o protocolo iSCSI ou Bloco de Mensagens do Servidor (SMB). Funciona na sua infraestrutura de hipervisor existente e fornece tiering para a nuvem, backup de nuvem, restauro rápido, recuperação de nível de item e recursos de recuperação de desastres.

A tabela seguinte resume as características importantes do StorSimple Virtual Array.

| Funcionalidade | Matriz Virtual do StorSimple |
| --- | --- |
| Requisitos de instalação |Utiliza infraestrutura de virtualização (Hyper-V ou VMware) |
| Disponibilidade |Nó único |
| Capacidade total (incluindo nuvem) |Até 64 TB de capacidade utilizável por matriz virtual |
| Capacidade local |390 GB a 6,4 TB de capacidade utilizável por matriz virtual (necessidade de provisão de 500 GB a 8 TB do espaço do disco) |
| Protocolos nativos |iSCSI ou SMB |
| Objetivo de tempo de recuperação (RTO) |iSCSI: menos de 2 minutos independentemente do tamanho |
| Objetivo de ponto de recuperação (RPO) |Backups diários e backups a pedido |
| Tiering de armazenamento |Usa mapeamento de calor para determinar que dados devem ser nivelados dentro ou fora |
| Suporte |Infraestrutura de virtualização apoiada pelo fornecedor |
| Desempenho |Varia dependendo da infraestrutura subjacente |
| Mobilidade de dados |Pode restaurar o mesmo dispositivo ou fazer recuperação ao nível do item (servidor de ficheiros) |
| Camadas de armazenamento |Armazenamento e nuvem de hipervisor local |
| Tamanho da partilha |Tiered: até 20 TB; fixado localmente: até 2 TB |
| Tamanho do volume |Tiered: 500 GB a 5 TB; fixado localmente: 50 GB a 200 GB <br> A reserva local máxima para volumes hierárquicos é de 200 GB. |
| Instantâneos |Consistente com a falha |
| Recuperação ao nível do item |Sim, o que é isto? os utilizadores podem restaurar a partir de ações |

## <a name="why-use-storsimple"></a>Porquê usar o StorSimple?

O StorSimple liga os utilizadores e servidores ao armazenamento do Azure em minutos, sem modificação da aplicação.

A tabela seguinte descreve alguns dos principais benefícios que a solução StorSimple Virtual Array proporciona.

| Funcionalidade | Vantagem |
| --- | --- |
| Integração transparente |O array virtual suporta o iSCSI ou o protocolo SMB. O movimento de dados entre o nível local e o nível de nuvem é perfeito e transparente para o utilizador. |
| Custos de armazenamento reduzidos |Com o StorSimple, você fornece armazenamento local suficiente para atender às exigências atuais dos dados quentes mais utilizados. À medida que as necessidades de armazenamento crescem, os dados de frio storSimple em armazenamento de nuvem rentável. Os dados são desduplicados e comprimidos antes de enviar para a nuvem para reduzir ainda mais os requisitos de armazenamento e despesas. |
| Gestão simplificada do armazenamento |O StorSimple fornece uma gestão centralizada na nuvem utilizando o StorSimple Device Manager para gerir vários dispositivos. |
| Melhoria da recuperação e da conformidade com as catástrofes |O StorSimple facilita uma recuperação mais rápida de desastres, restaurando imediatamente os metadados e restaurando os dados conforme necessário. As operações normais podem continuar com a interrupção mínima. |
| Mobilidade de dados |Os dados hierárquicos para a nuvem podem ser acedidos a partir de outros sites para fins de recuperação e migração. Só pode restaurar os dados para a matriz virtual original. No entanto, você usa funcionalidades de recuperação de desastres para restaurar toda a matriz virtual para outra matriz virtual. |



## <a name="storsimple-workload-summary"></a>Resumo da carga de trabalho StorSimple

Um resumo das cargas de trabalho StorSimple suportadas é tabulado abaixo.

|Scenario     |Carga de trabalho     |Suportado      |Restrições               | Versões aplicáveis|
|-------------|-------------|---------------|---------------------------|--------------------|
|Escritório remoto/sucursal (ROBO)  |Partilha de ficheiros     |Yes      |Consulte [os limites máximos para o servidor de ficheiros.](storsimple-ova-limits.md)<br></br>Consulte [os requisitos do sistema para versões SMB suportadas](storsimple-ova-system-requirements.md).| Todas as versões     |
|Arquivo de nuvens  |Partilha de arquivo     |Yes      |Consulte [os limites máximos para o servidor de ficheiros.](storsimple-ova-limits.md)<br></br>Consulte [os requisitos do sistema para versões SMB suportadas](storsimple-ova-system-requirements.md).| Todas as versões     |

O StorSimple Virtual Array é o mais adequado para dados pouco acedidos. Embora a matriz virtual tenha uma cache local para aumentar o desempenho, os utilizadores devem assumir que os ficheiros de serviços do dispositivo no nível mais baixo de armazenamento (a nuvem). Cada matriz virtual pode escrever e ler para o armazenamento Azure a aproximadamente 100 Mbps. Esta ligação é partilhada em todos os pedidos que entram no dispositivo e pode tornar-se um estrangulamento, como mostrado no diagrama abaixo.

![Arquivo de nuvens](./media/storsimple-ova-overview/cloud-archiving.png)

Quando vários utilizadores simultâneos acedem à matriz virtual, todos partilham a ligação com o Azure, levando a um desempenho mais baixo. Não existe um desempenho garantido por utilizador, e o dispositivo processa os pedidos individuais à medida que chegam.

O StorSimple Virtual Array não é adequado para cargas de trabalho que exijam alta disponibilidade. O conjunto virtual é um dispositivo de nó único que experimenta tempo de inatividade quando as atualizações de software são instaladas. Os administradores devem planear uma janela de manutenção de 30 minutos 3-4 vezes por ano.

## <a name="workflows"></a>Fluxos de trabalho

O StorSimple Virtual Array é particularmente adequado para os seguintes fluxos de trabalho:

* [Gestão de armazenamento baseada em nuvem](#cloud-based-storage-management)
* [Backup independente de localização](#location-independent-backup)
* [Proteção de dados e recuperação de desastres](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Gestão de armazenamento baseada em nuvem
Pode utilizar o serviço StorSimple Device Manager em execução no portal Azure para gerir dados armazenados em vários dispositivos e em vários locais. Isto é particularmente útil em cenários de filiais distribuídas. Deve criar instâncias separadas do serviço StorSimple Device Manager para gerir matrizes virtuais e dispositivos StorSimple físicos. A matriz virtual agora usa o novo portal Azure em vez do portal clássico Azure.<!--Is the "now" element still in date? Could it go at this point? Just checking.-->

![gestão de armazenamento baseada em nuvem](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Backup independente de localização
Com a matriz virtual, as imagens em nuvem fornecem uma cópia de um volume ou partilha independente de localização. As imagens em nuvem são ativadas por padrão e não podem ser desativadas. Todos os volumes e ações são apoiados ao mesmo tempo através de uma única política diária de backup, e você pode tomar backups ad hoc adicionais sempre que necessário.

### <a name="data-protection-and-disaster-recovery"></a>Proteção de dados e recuperação de desastres
O conjunto virtual suporta os seguintes cenários de proteção de dados e recuperação de desastres:

* **Restauro de volume ou partilha** – Utilize o restauro como novo fluxo de trabalho para recuperar um volume ou uma partilha. Utilize esta abordagem para recuperar todo o volume ou partilha.
* **Recuperação de nível de item** – As ações permitem o acesso simplificado a cópias de segurança recentes. Pode facilmente recuperar um ficheiro individual a partir de uma pasta especial *.backup* disponível na nuvem. Esta capacidade de restauro é orientada pelo utilizador e não é necessária qualquer intervenção administrativa.
* **Recuperação de desastres** – Utilize a capacidade de failover para recuperar todos os volumes ou partilhas para um novo conjunto virtual. Cria a nova matriz virtual e regista-a com o serviço StorSimple Device Manager e depois falha na matriz virtual original. O novo conjunto virtual assumirá então os recursos a provisionados.

## <a name="storsimple-virtual-array-components"></a>Componentes StorSimple Virtual Array

A matriz virtual inclui os seguintes componentes:

* [Matriz virtual](#virtual-array) – Um dispositivo híbrido de armazenamento em nuvem baseado numa máquina virtual aprovisionada no seu ambiente virtualizado ou hipervisor.
* [Serviço StorSimple Device Manager](#storsimple-device-manager-service) – Uma extensão do portal Azure que permite gerir um ou mais dispositivos StorSimple a partir de uma única interface web que pode aceder a partir de diferentes localizações geográficas. Pode utilizar o serviço StorSimple Device Manager para criar e gerir serviços, visualizar e gerir dispositivos e alertas e gerir volumes, partilhas e instantâneos existentes.
* [Interface de utilizador web local](#local-web-user-interface) – Um UI baseado na Web usado para configurar o dispositivo para que possa ligar-se à rede local e, em seguida, registar o dispositivo com o serviço StorSimple Device Manager. 
* [Interface de linha de comando](#command-line-interface) – uma interface Windows PowerShell que pode utilizar para iniciar uma sessão de suporte no conjunto virtual.
  As secções seguintes descrevem cada componente com maior detalhe e explicam como a solução organiza dados, atribui armazenamento e facilita a gestão do armazenamento e proteção de dados.

### <a name="virtual-array"></a>Matriz virtual

A matriz virtual é uma solução de armazenamento de nó único que fornece armazenamento primário, gere a comunicação com o armazenamento em nuvem, e ajuda a garantir a segurança e confidencialidade de todos os dados armazenados no dispositivo.

O conjunto virtual está disponível num modelo que está disponível para download. A matriz virtual tem uma capacidade máxima de 6.4 TB no dispositivo (com um requisito de armazenamento subjacente de 8 TB) e 64 TB, incluindo armazenamento em nuvem.

A matriz virtual tem as seguintes características:

* É rentável. Utiliza a sua infraestrutura de virtualização existente e pode ser implantada no hipervisor Hiper-V ou VMware existente.
* Reside no datacenter e pode ser configurado como um servidor iSCSI ou um servidor de ficheiros.
* Está integrado com a nuvem.
* As cópias de segurança são armazenadas na nuvem, o que pode facilitar a recuperação de desastres e simplificar a recuperação do nível do item (ILR).
* Pode aplicar atualizações na matriz virtual, tal como as aplicaria a um dispositivo físico.

> [!NOTE]
> Uma matriz virtual não pode ser expandida. Portanto, é importante providenciar armazenamento adequado quando se cria a matriz virtual.

### <a name="storsimple-device-manager-service"></a>Serviço de Gestor de Dispositivos do StorSimple

O Microsoft Azure StorSimple fornece uma interface de utilizador baseada na Web, o serviço StorSimple Device Manager, que lhe permite gerir centralmente o armazenamento StorSimple. Pode utilizar o serviço StorSimple Device Manager para executar as seguintes tarefas:

* Gerencie vários Arrays Virtuais StorSimple a partir de um único serviço.
* Configure e gere as definições de segurança para StorSimple Arrays virtuais. (A encriptação na nuvem depende das APIs do Microsoft Azure.)
* Configure credenciais e propriedades de conta de armazenamento.
* Configure e gere volumes ou ações.
* Fazer o back up e restaurar os dados sobre volumes ou ações.
* Monitorize o desempenho.
* Rever as definições do sistema e identificar possíveis problemas.

Utiliza o serviço StorSimple Device Manager para fazer a administração diária da sua matriz virtual.

Para obter mais informações, [aceda ao serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-virtual-array-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Interface de utilizador web local

O array virtual inclui uma UI baseada na web que é usada para configuração e registo único do dispositivo com o serviço StorSimple Device Manager. Pode usá-lo para desligar e reiniciar a matriz virtual, executar testes de diagnóstico, atualizar software, alterar a palavra-passe do administrador do dispositivo, ver registos do sistema e contactar o Microsoft Support para arquivar um pedido de serviço.

Para obter informações sobre a utilização da UI baseada na Web, vá ao [UI baseado na web para administrar o seu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Interface de linha de comando

A interface incluída no Windows PowerShell permite-lhe iniciar uma sessão de suporte com o Microsoft Support para que possa ajudá-lo a resolver problemas e resolver problemas que poderá encontrar na sua matriz virtual.

## <a name="storage-management-technologies"></a>Tecnologias de gestão de armazenamento

Além da matriz virtual e de outros componentes, a solução StorSimple utiliza as seguintes tecnologias de software para fornecer acesso rápido a dados importantes, reduzir o consumo de armazenamento e proteger os dados que são armazenados na sua matriz virtual:

* [Tiering automático de armazenamento](#automatic-storage-tiering) 
* [Ações e volumes fixados localmente](#locally-pinned-shares-and-volumes)
* Deduplicação e compressão para dados tiered ou apoiados até à nuvem 
* [Backups programados e a pedido](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Tiering automático de armazenamento
A matriz virtual usa um novo mecanismo de tiering para gerir dados armazenados através da matriz virtual e da nuvem. Existem apenas dois níveis: a matriz virtual local e o armazenamento em nuvem Azure. O StorSimple Virtual Array organiza automaticamente dados nos níveis baseados num mapa de calor, que rastreia o uso, idade e relacionamentos atuais com outros dados. Os dados mais ativos (mais quentes) são armazenados localmente, enquanto dados menos ativos e inativos são automaticamente migrados para a nuvem. (Todas as cópias de segurança são armazenadas na nuvem.) StorSimple ajusta e reorganiza as atribuições de dados e armazenamento à medida que os padrões de utilização mudam. Por exemplo, algumas informações podem tornar-se menos ativas com o tempo. À medida que se torna progressivamente menos ativo, é hierárquico para a nuvem. Se esses mesmos dados voltarem a estar ativos, é hierárquico para a matriz de armazenamento.

Os dados relativos a uma determinada quota ou volume hierárquico são garantidos ao seu próprio espaço de nível local (aproximadamente 10% do espaço total previsto para essa parte ou volume). Embora isso reduza o armazenamento disponível na matriz virtual para essa partilha ou volume, garante que o tiering para uma parte ou volume não será afetado pelas necessidades de nivelamento de outras ações ou volumes. Assim, uma carga de trabalho muito movimentada numa parte ou volume não pode forçar todas as outras cargas de trabalho para a nuvem.

Os volumes hierárquicos criados para o iSCSI têm uma reserva local máxima de 200 GB, independentemente do tamanho do volume.

![nivelamento automático de armazenamento](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> Pode especificar um volume como fixado localmente, caso em que os dados permanecem na matriz virtual e nunca são nivelados para a nuvem. Para mais informações, aceda a [ações e volumes fixados localmente.](#locally-pinned-shares-and-volumes)

> [!IMPORTANT]
> Quando utilizar o StorSimple, não converta as bolhas em arquivo, mesmo que o seu dispositivo esteja a ser eliminado gradualmente. Para obter dados do dispositivo, terá de reidratar as bolhas do arquivo para o tipo quente ou fresco, o que resulta em custos significativos.


### <a name="locally-pinned-shares-and-volumes"></a>Ações e volumes fixados localmente

Pode criar ações e volumes apropriados como fixados localmente. Esta capacidade garante que os dados exigidos por aplicações críticas permanecem na matriz virtual e nunca são hierárquicos para a nuvem. As ações e volumes fixados localmente têm as seguintes características:

* Não estão sujeitos a atrasos em nuvem ou problemas de conectividade.
* Eles ainda beneficiam de funcionalidades de backup de nuvem StorSimple e recursos de recuperação de desastres.

Pode restaurar uma parte ou volume fixado localmente como tiered ou uma parte ou volume hierarquizado como fixado localmente. 

Para obter mais informações sobre volumes fixados localmente, vá utilizar [o serviço StorSimple Device Manager para gerir volumes](storsimple-virtual-array-manage-volumes.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Deduplicação e compressão para dados tiered ou apoiados até à nuvem

A StorSimple utiliza a deduplicação e a compressão de dados para reduzir ainda mais os requisitos de armazenamento na nuvem. A deduplica reduz a quantidade global de dados armazenados eliminando a redundância no conjunto de dados armazenados. À medida que a informação muda, o StorSimple ignora os dados inalterados e captura apenas as alterações. Além disso, a StorSimple reduz a quantidade de dados armazenados identificando e removendo informações duplicadas.

> [!NOTE]
> Os dados armazenados na matriz virtual não são desduplicados ou comprimidos. Toda a desduplicação e compressão ocorre pouco antes de os dados serem enviados para a nuvem.


### <a name="scheduled-and-on-demand-backups"></a>Backups programados e a pedido

As funcionalidades de proteção de dados StorSimple permitem criar cópias de segurança a pedido. Além disso, um horário de backup predefinido garante que os dados são cópias de segurança diárias. As cópias de segurança são tomadas sob a forma de instantâneos incrementais, que são armazenados na nuvem. As imagens instantâneas, que registam apenas as alterações desde a última cópia de segurança, podem ser criadas e restauradas rapidamente. Estes instantâneos podem ser de importância crítica em cenários de recuperação de desastres porque substituem sistemas de armazenamento secundário (como cópia de segurança de fita), e permitem restaurar os dados no seu datacenter ou em sites alternativos, se necessário.

## <a name="managing-personal-information"></a>Gestão de informações pessoais

O Gestor de Dispositivos StorSimple para séries virtuais recolhe informações pessoais em dois casos-chave:
 - Alerte as definições do utilizador onde os endereços de e-mail dos utilizadores estão configurados. Esta informação pode ser apurada pelo administrador. 
 - Utilizadores que possam aceder aos dados sobre as partilhas. Uma lista de utilizadores que podem aceder aos dados da partilha é apresentada e pode ser exportada. Esta lista é eliminada quando a partilha é eliminada.

Para mais informações, reveja a política de privacidade da [Microsoft no Trust Center.](https://www.microsoft.com/trustcenter)

## <a name="next-steps"></a>Passos seguintes

Saiba como [preparar o portal de matrizes virtuais.](storsimple-virtual-array-deploy1-portal-prep.md)
