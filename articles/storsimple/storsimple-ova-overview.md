---
title: Descrição geral do Microsoft Azure StorSimple Virtual Array | Documentos da Microsoft
description: Descreve a matriz Virtual StorSimple, uma solução de armazenamento integrada que gere tarefas de armazenamento entre uma matriz virtual no local e o armazenamento do Microsoft Azure na cloud.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 169c639b-1124-46a5-ae69-ba9695525b77
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: e5713af737a6d9d190814b4155a8e772deea06bc
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58013906"
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>Introdução ao StorSimple Virtual Array

## <a name="overview"></a>Descrição geral

O Microsoft Azure StorSimple Virtual Array é uma solução de armazenamento integrada que gere tarefas de armazenamento entre uma matriz de virtual no local em execução num hipervisor e o armazenamento do Microsoft Azure na cloud. A matriz virtual é um servidor de ficheiros eficiente, rentável e facilmente geridas ou a solução de servidor de iSCSI que elimina muitos dos problemas e despesas associadas a proteção de armazenamento e dados da empresa. A matriz virtual é particularmente adequada para o armazenamento de dados de arquivo acedidos com pouca frequência.

Este artigo fornece uma descrição geral da matriz virtual - aqui estão alguns outros recursos:

* Para melhores práticas, consulte [práticas recomendadas do StorSimple Virtual Array](storsimple-ova-best-practices.md).
* Para uma descrição geral dos dispositivos de série StorSimple 8000, aceda a [série StorSimple 8000: uma solução de cloud híbrida](storsimple-overview.md).
* Para obter informações sobre os dispositivos da série StorSimple 5000/7000, aceda a [a Ajuda Online do StorSimple](http://onlinehelp.storsimple.com/).

A matriz virtual suporta o protocolo bloco de mensagem de servidor (SMB) ou iSCSI. Ele é executado na sua infraestrutura existente do hipervisor e fornece a disposição em camadas para a nuvem, cópia de segurança na cloud, restauro rápido, recuperação ao nível do item e recursos de recuperação após desastre.

A tabela seguinte resume as funcionalidades importantes da matriz Virtual StorSimple.

| Funcionalidade | Matriz Virtual do StorSimple |
| --- | --- |
| Requisitos de instalação |Utiliza a infraestrutura de Virtualização (Hyper-V ou VMware) |
| Disponibilidade |Nó único |
| Capacidade total (incluindo na nuvem) |Capacidade utilizável de TB até 64 por matriz virtual |
| Capacidade local |390 GB a capacidade utilizável de 6.4 TB por matriz virtual (é necessário aprovisionar 500 GB para 8 TB de espaço em disco) |
| Protocolos nativos |iSCSI ou SMB |
| Objetivo de tempo de recuperação (RTO) |iSCSI: inferior a 2 minutos, independentemente do tamanho |
| Objetivo de ponto de recuperação (RPO) |Cópias de segurança diárias e cópias de segurança a pedido |
| Armazenamento em camadas |Utiliza o mapeamento para determinar quais os dados devem ser colocado em camadas dentro ou para fora de calor |
| Suporte |Infraestrutura de Virtualização suportada pelo fornecedor |
| Desempenho |Varia consoante a infraestrutura subjacente |
| Mobilidade de dados |Pode restaurar para o mesmo dispositivo ou ao nível do item recovery (servidor de ficheiros) |
| Camadas de armazenamento |Armazenamento de hipervisor local e na cloud |
| Tamanho da partilha |Camadas: até 20 TB; afixado localmente: até 2 TB |
| Tamanho do volume |Camadas: 500 GB para 5 TB; afixado localmente: 50 GB e 200 GB <br> Reserva local máxima de volumes em camadas é 200 GB. |
| Instantâneos |Consistente com a falha |
| Recuperação ao nível do item |Sim; os utilizadores podem restaurar a partir de partilhas |

## <a name="why-use-storsimple"></a>Porquê utilizar o StorSimple?

StorSimple liga-se os utilizadores e os servidores ao armazenamento do Azure em minutos, sem modificações de aplicação.

A tabela seguinte descreve algumas das principais vantagens que a solução StorSimple Virtual Array fornece.

| Funcionalidade | Vantagem |
| --- | --- |
| Integração transparente |A matriz virtual suporta o iSCSI ou o protocolo SMB. O movimento de dados entre a camada de local e o escalão de nuvem é direta e transparente para o usuário. |
| Custos de armazenamento reduzidos |Com o StorSimple, aprovisionar armazenamento local suficiente para atender às demandas atuais para os dados de acesso frequente mais utilizados. Conforme as necessidades de armazenamento crescer, dados de frios escalões do StorSimple económica para armazenamento na nuvem. Os dados são com eliminação de duplicados e comprimidos antes de enviar para a cloud para reduzir ainda mais os requisitos de armazenamento e a despesa. |
| Gestão de armazenamento simplificado |O StorSimple Fornece gerenciamento centralizado na cloud com o Gestor de dispositivos do StorSimple para gerir vários dispositivos. |
| Recuperação de desastres aprimorados e conformidade |StorSimple facilita a recuperação de desastre mais rápida, restaurar os metadados imediatamente e restaurar os dados conforme necessário. Isso significa que as operações normais podem continuar com a mínima interrupção. |
| Mobilidade de dados |Dados em camadas para a cloud pode ser acedido a partir de outros sites para fins de recuperação e a migração. Tenha em atenção que pode restaurar os dados apenas para a matriz virtual original. No entanto, utilize os recursos de recuperação de desastres para restaurar a matriz virtual completa para uma matriz. |

## <a name="storsimple-workload-summary"></a>Resumida da carga de trabalho do StorSimple

Um resumo das cargas de trabalho suportadas do StorSimple é apresentado abaixo.

|Cenário     |Carga de trabalho     |Suportadas      |Restrições               | Versões aplicáveis|
|-------------|-------------|---------------|---------------------------|--------------------|
|Office de sucursais/escritórios remotos (ROBO)  |Partilha de ficheiros     |Sim      |Ver [limites máximos para servidor de ficheiros](storsimple-ova-limits.md).<br></br>Ver [requisitos de sistema para versões suportadas do SMB](storsimple-ova-system-requirements.md).| Todas as versões     |
|Arquivamento da cloud  |Partilha de ficheiros de arquivo     |Sim      |Ver [limites máximos para servidor de ficheiros](storsimple-ova-limits.md).<br></br>Ver [requisitos de sistema para versões suportadas do SMB](storsimple-ova-system-requirements.md).| Todas as versões     |

O StorSimple Virtual Array é mais adequado para dados acedidos com pouca frequência. Enquanto que a matriz virtual tem um cache local para aumentar o desempenho, os utilizadores devem assumir que o dispositivo dos serviços de ficheiros no escalão mais baixo de armazenamento (cloud). Cada matriz virtual pode escrever e ler para o armazenamento do Azure em cerca de 100 Mbps. Essa ligação é partilhada entre todos os pedidos para o dispositivo e pode se tornar um afunilamento conforme mostrado no diagrama abaixo.

![Arquivamento da cloud](./media/storsimple-ova-overview/cloud-archiving.png)

Quando vários utilizadores em simultâneo acedem a matriz virtual, todos eles partilhar a ligação para o Azure para um desempenho inferior. Não existe nenhum desempenho garantido por utilizador e o dispositivo processa solicitações individuais à medida que chegam.

StorSimple Virtual Array não é adequado para cargas de trabalho que exijam a elevada disponibilidade. A matriz virtual é um dispositivo de nó único que ocorre no período de indisponibilidade quando as atualizações de software estão instaladas. Os administradores devem planejar uma janela de manutenção de 30 minutos 3 - 4 vezes por ano.

## <a name="workflows"></a>Fluxos de trabalho

O StorSimple Virtual Array é particularmente adequado para os fluxos de trabalho seguintes:

* [Gestão de armazenamento baseado na nuvem](#cloud-based-storage-management)
* [Cópia de segurança independente do local](#location-independent-backup)
* [Recuperação após desastre e de proteção de dados](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Gestão de armazenamento baseado na nuvem
Pode utilizar o serviço de Gestor de dispositivos do StorSimple em execução no portal do Azure para gerir os dados armazenados em vários dispositivos e em vários locais. Isto é particularmente útil em cenários de ramificação distribuída. Tenha em atenção que tem de criar instâncias separadas do serviço StorSimple Device Manager para gerir as matrizes virtuais e os dispositivos físicos do StorSimple. Observe também que a matriz virtual utiliza agora o novo portal do Azure em vez do portal clássico do Azure.

![Gestão de armazenamento baseado na nuvem](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Cópia de segurança independente do local
Com a matriz virtual, instantâneos de cloud fornecem uma cópia independentemente da localização, o ponto no tempo de um volume ou partilha. Instantâneos de cloud estão ativados por predefinição e não podem ser desativados. Todos os volumes e partilhas são cópia de segurança ao mesmo tempo por meio de uma única política de cópia de segurança diária, e que pode realizar backups ad hoc adicionais sempre que necessário.

### <a name="data-protection-and-disaster-recovery"></a>Recuperação após desastre e de proteção de dados
A matriz virtual suporta os seguintes de proteção de dados e cenários de recuperação de desastres:

* **Restauro de volume ou partilha** – utilize o restauro como novo fluxo de trabalho para recuperar um volume ou partilha. Utilize esta abordagem para recuperar o volume inteiro ou uma partilha.
* **Recuperação a nível de item** – partilhas permitem acesso simplificado às cópias de segurança recentes. Pode recuperar facilmente um arquivo individual de um especial *.backup* pasta disponível na cloud. Esta capacidade de restauro é controlada pelo usuário e é necessária nenhuma intervenção administrativa.
* **Recuperação após desastre** – utilizar a capacidade de ativação pós-falha para recuperar todos os volumes ou compartilhamentos para uma nova matriz virtual. Criar a nova matriz virtual e registá-lo com o serviço StorSimple Device Manager, e efetuar a ativação pós-falha a matriz virtual original. A nova matriz virtual, em seguida, supor que os recursos aprovisionados.

## <a name="storsimple-virtual-array-components"></a>Componentes de matriz Virtual StorSimple

A matriz virtual inclui os seguintes componentes:

* [Matriz virtual](#virtual-array) – um dispositivo de armazenamento na cloud híbrida com base numa máquina virtual aprovisionada no seu ambiente virtualizado ou hipervisor.
* [Serviço StorSimple Device Manager](#storsimple-device-manager-service) -uma extensão do portal do Azure que lhe permite gerir um ou mais dispositivos do StorSimple a partir de uma interface web individual que pode aceder a partir de localizações geográficas diferentes. Pode utilizar o serviço StorSimple Device Manager para criar e gerir serviços, ver e gerir dispositivos e alertas e gerir volumes, partilhas e instantâneos existentes.
* [Interface de usuário da local web](#local-web-user-interface) – uma interface de Usuário baseada na web, que é utilizado para configurar o dispositivo para que possam ligar à rede local e, em seguida, registar o dispositivo com o serviço StorSimple Device Manager. 
* [Interface de linha de comandos](#command-line-interface) – interface de um Windows PowerShell que pode utilizar para iniciar uma sessão de suporte na matriz virtual.
  As secções seguintes descrevem cada um desses componentes em mais detalhes e explicam como a solução organiza os dados, aloca o armazenamento e facilita a gestão de armazenamento e proteção de dados.

### <a name="virtual-array"></a>Matriz virtual

A matriz virtual é uma solução de armazenamento de nó único, que fornece armazenamento primário, gerencia a comunicação com o armazenamento na cloud e ajuda a garantir a segurança e a confidencialidade de todos os dados armazenados no dispositivo.

A matriz virtual está disponível num modelo que está disponível para download. A matriz virtual tem a capacidade máxima de 6.4 TB no dispositivo (com um requisito de armazenamento subjacente de 8 TB) e 64 TB incluindo armazenamento na nuvem.

A matriz virtual tem as seguintes funcionalidades:

* É rentável. Ele faz uso de sua infraestrutura de Virtualização existente e pode ser implementado em seu hipervisor Hyper-V ou VMware existente.
* Ele reside no Centro de dados e pode ser configurado como um servidor de iSCSI ou de um servidor de ficheiros.
* Ele está integrado com a cloud.
* As cópias de segurança são armazenadas na cloud, que pode facilitar a recuperação após desastre e simplificar a recuperação ao nível do item (ILR).
* Pode aplicar atualizações para a matriz virtual, tal como faria aplicá-las para um dispositivo físico.

> [!NOTE]
> Uma matriz virtual não pode ser expandida. Portanto, é importante aprovisionar armazenamento adequado quando cria a matriz virtual.

### <a name="storsimple-device-manager-service"></a>Serviço do Gestor de Dispositivos do StorSimple

O Microsoft Azure StorSimple fornece uma interface do usuário baseada na web, o serviço StorSimple Device Manager, que permite-lhe gerir centralmente o armazenamento do StorSimple. Pode utilizar o serviço StorSimple Device Manager para efetuar as seguintes tarefas:

* Gerir várias matrizes virtuais do StorSimple a partir de um único serviço.
* Configurar e gerir definições de segurança para as matrizes virtuais do StorSimple. (Encriptação na cloud é dependente de APIs do Microsoft Azure.)
* Configure as credenciais da conta de armazenamento e as propriedades.
* Configurar e gerir volumes ou compartilhamentos.
* Criar cópias de segurança e restaurar dados em volumes ou compartilhamentos.
* Monitorizar o desempenho.
* Reveja as definições do sistema e identificar possíveis problemas.

Utilizar o serviço StorSimple Device Manager para executar a administração diária da sua matriz virtual.

Para obter mais informações, aceda a [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-virtual-array-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Interface de usuário da local web

A matriz virtual inclui uma interface de Usuário baseada na web, que é utilizado para a configuração de uso individual e o registo do dispositivo com o serviço StorSimple Device Manager. Pode usá-lo para encerrar e reiniciar a matriz virtual, execute testes de diagnóstico, atualização de software, alterar a palavra-passe de administrador do dispositivo, ver registos de sistema e entre em contato com Support da Microsoft para um pedido de serviço de ficheiros.

Para informações sobre como utilizar a interface do Usuário baseada na web, aceda a [utilizar a interface do Usuário baseada na web para administrar a sua matriz Virtual StorSimple](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Interface de linha de comandos

A interface do Windows PowerShell incluída permite-lhe iniciar uma sessão de suporte com Support da Microsoft para que eles podem ajudá-lo a resolver problemas que poderá encontrar na sua matriz virtual.

## <a name="storage-management-technologies"></a>Tecnologias de gerenciamento de armazenamento

Além da matriz virtual e outros componentes, a solução StorSimple usa as seguintes tecnologias de software para fornecer acesso rápido a dados importantes, reduzir o consumo de armazenamento e proteger os dados armazenados na sua matriz virtual:

* [A criação de camadas de armazenamento automática](#automatic-storage-tiering) 
* [Afixado localmente compartilhamentos e volumes](#locally-pinned-shares-and-volumes)
* A eliminação de duplicados e compressão de dados em camadas ou uma cópia de segurança para a cloud 
* [Cópias de segurança agendadas e sob demanda](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>A criação de camadas de armazenamento automática
A matriz virtual utiliza um novo mecanismo de camadas para gerir dados armazenados em toda a matriz virtual e a cloud. Existem apenas dois escalões: a matriz virtual local e o Azure armazenamento na nuvem. O StorSimple Virtual Array organiza automaticamente dados em camadas com base num mapa de calor, que controla a utilização atual, a idade e relações a outros dados. Os dados que são mais ativo (mais interessantes) é armazenada localmente, enquanto os dados menos ativos e inativos é migrados automaticamente para a cloud. (Todas as cópias de segurança são armazenadas na nuvem.) StorSimple ajusta e reorganiza a dados e alterar as atribuições de armazenamento como padrões de utilização. Por exemplo, algumas informações podem tornar-se menos ativas ao longo do tempo. Como ele se torna cada vez menos ativo, é camada horizontalmente para a cloud. Se esses mesmos dados é novamente ativos, é camada à matriz de armazenamento.

Dados de uma determinada partilha em camadas ou um volume são garantidos que seu próprio espaço de escalão local (aproximadamente 10% do total de espaço aprovisionado para esse compartilhamento ou volume). Enquanto isso reduz o armazenamento disponível na matriz virtual para esse compartilhamento ou volume, ele garante que disposição em camadas para uma partilha ou volume não será afetada pelas necessidades camadas das outras partilhas ou volumes. Portanto, uma carga de trabalho muito ocupada num compartilhamento ou volume não pode exigir todas as outras cargas de trabalho para a cloud.

Volumes em camadas, criados para iSCSI tem uma reserva local máxima de 200 GB, independentemente do tamanho do volume.

![A criação de camadas de armazenamento automática](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> Pode especificar um volume afixado localmente como, caso em que os dados permanecem na matriz virtual e nunca é em camadas para a cloud. Para obter mais informações, aceda a [localmente afixado compartilhamentos e volumes](#locally-pinned-shares-and-volumes).


### <a name="locally-pinned-shares-and-volumes"></a>Afixado localmente compartilhamentos e volumes

Pode criar partilhas apropriadas e volumes afixados localmente como. Esta capacidade garante que os dados exigidos por aplicativos críticos permanecem na matriz virtual e nunca é em camadas para a cloud. Afixado localmente compartilhamentos e volumes têm as seguintes funcionalidades:

* Não estão sujeitas à cloud latências ou problemas de conectividade.
* Eles ainda se beneficiar da StorSimple cloud após desastre e cópia de segurança recursos de recuperação.

Pode restaurar uma partilha de afixado localmente ou como camadas de volume ou uma partilha em camadas ou volume como localmente afixado. 

Para obter mais informações sobre os volumes afixados localmente, aceda a [utilizar o serviço StorSimple Device Manager para gerir volumes](storsimple-virtual-array-manage-volumes.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>A eliminação de duplicados e compressão de dados em camadas ou uma cópia de segurança para a cloud

O StorSimple utiliza a compactação de dados e de eliminação de duplicados para reduzir ainda mais os requisitos de armazenamento na cloud. A eliminação de duplicados reduz a quantidade global dos dados armazenados ao eliminar a redundância no conjunto de dados armazenado. Que informações é alterado, o StorSimple ignora os dados inalterados e captura apenas as alterações. Além disso, o StorSimple reduz a quantidade de dados armazenados ao identificar e remover informações duplicadas.

> [!NOTE]
> Dados armazenados na matriz virtual não está com eliminação de duplicados ou comprimidos. Todos da eliminação de duplicados e compressão ocorre imediatamente antes dos dados são enviados para a cloud.

### <a name="scheduled-and-on-demand-backups"></a>Cópias de segurança agendadas e sob demanda

Funcionalidades de proteção de dados do StorSimple permitem-lhe criar cópias de segurança a pedido. Além disso, uma agenda de cópia de segurança predefinida assegura que dados de cópia de segurança diária. Backups são feitos na forma de instantâneos incrementais, o que são armazenados na cloud. Instantâneos, o que gravar apenas as alterações desde a última cópia de segurança, podem ser criados e restaurados rapidamente. Estes instantâneos podem ser extremamente importantes em cenários de recuperação após desastre, pois substituir sistemas de armazenamento secundário (por exemplo, a cópia de segurança de banda) e permitem-lhe restaurar dados para o seu datacenter ou para sites alternativos, se necessário.

## <a name="managing-personal-information"></a>Gerenciamento de informações pessoais

O StorSimple Device Manager para a série virtual recolhe as informações pessoais em duas instâncias de chaves:
 - Definições de utilizador onde os endereços de e-mail dos utilizadores estão configurados de alerta. Estas informações podem ser desmarcadas pelo administrador. 
 - Utilizadores que podem aceder os dados que residem nas partilhas de. Uma lista de utilizadores que podem aceder os dados de partilha é apresentada e pode ser exportada. Esta lista também é eliminada quando as partilhas é eliminado.

Para obter mais informações, reveja os [política de Privacy da Microsoft no Centro de fidedignidade](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Passos Seguintes

Saiba como [preparar o portal de matriz virtual](storsimple-virtual-array-deploy1-portal-prep.md).
