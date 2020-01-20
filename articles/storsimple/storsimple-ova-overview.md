---
title: Visão geral de Microsoft Azure StorSimple matriz virtual
description: Descreve a matriz virtual StorSimple, uma solução de armazenamento integrado que gerencia tarefas de armazenamento entre uma matriz virtual local e Microsoft Azure armazenamento em nuvem.
author: alkohli
ms.assetid: 169c639b-1124-46a5-ae69-ba9695525b77
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 32781a83aec996b23f161f5fe695f39a0de38685
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76273876"
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>Introdução à matriz virtual StorSimple

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Visão geral

A matriz virtual Microsoft Azure StorSimple é uma solução de armazenamento integrada que gerencia tarefas de armazenamento entre uma matriz virtual local em execução em um hipervisor e Microsoft Azure armazenamento em nuvem. A matriz virtual é uma solução de servidor de arquivos eficiente, econômica e fácil de gerenciar, que elimina muitos dos problemas e despesas associados ao armazenamento corporativo e à proteção de dados. A matriz virtual é particularmente adequada para o armazenamento de dados de arquivamento acessados com pouca frequência.

Este artigo fornece uma visão geral da matriz virtual – aqui estão alguns outros recursos:

* Para obter as práticas recomendadas, consulte [práticas recomendadas do StorSimple virtual array](storsimple-ova-best-practices.md).
* Para obter uma visão geral dos dispositivos StorSimple da série 8000, vá para [StorSimple 8000 Series: uma solução de nuvem híbrida](storsimple-overview.md).
* Para obter informações sobre os dispositivos StorSimple da série 5000/7000, acesse a [ajuda online do storsimple](http://onlinehelp.storsimple.com/).

A matriz virtual é compatível com o protocolo SMB ou servidor. Ele é executado em sua infraestrutura de hipervisor existente e fornece camadas para a nuvem, backup de nuvem, restauração rápida, recuperação em nível de item e recursos de recuperação de desastres.

A tabela a seguir resume os recursos importantes da matriz virtual StorSimple.

| Funcionalidade | Matriz Virtual StorSimple |
| --- | --- |
| Requisitos de instalação |Usa a infraestrutura de virtualização (Hyper-V ou VMware) |
| Disponibilidade |Nó único |
| Capacidade total (incluindo nuvem) |Até 64 TB de capacidade utilizável por matriz virtual |
| Capacidade local |390 GB a 6,4 TB de capacidade utilizável por matriz virtual (é necessário provisionar 500 GB a 8 TB de espaço em disco) |
| Protocolos nativos |iSCSI ou SMB |
| Objetivo de tempo de recuperação (RTO) |iSCSI: menos de 2 minutos, independentemente do tamanho |
| Objetivo de ponto de recuperação (RPO) |Backups diários e backups sob demanda |
| Camadas de armazenamento |Usa o mapeamento de calor para determinar quais dados devem ser dispostos em camadas ou fora |
| Suporte |Infraestrutura de virtualização com suporte do fornecedor |
| Desempenho |Varia dependendo da infraestrutura subjacente |
| Mobilidade de dados |Pode restaurar para o mesmo dispositivo ou fazer a recuperação em nível de item (servidor de arquivos) |
| Camadas de armazenamento |Armazenamento e nuvem do hipervisor local |
| Tamanho do compartilhamento |Em camadas: até 20 TB; localmente fixado: até 2 TB |
| Tamanho do volume |Em camadas: 500 GB a 5 TB; localmente fixado: 50 GB a 200 GB <br> A reserva local máxima para volumes em camadas é de 200 GB. |
| Instantâneos |Com consistência de falha |
| Recuperação ao nível do item |Ok os usuários podem restaurar de compartilhamentos |

## <a name="why-use-storsimple"></a>Por que usar o StorSimple?

O StorSimple conecta usuários e servidores ao armazenamento do Azure em minutos, sem nenhuma modificação de aplicativo.

A tabela a seguir descreve alguns dos principais benefícios que a solução StorSimple virtual array fornece.

| Funcionalidade | Beneficie |
| --- | --- |
| Integração transparente |A matriz virtual dá suporte ao protocolo iSCSI ou SMB. A movimentação de dados entre a camada local e a camada de nuvem é direta e transparente para o usuário. |
| Custos de armazenamento reduzidos |Com o StorSimple, você provisiona armazenamento local suficiente para atender às demandas atuais dos dados mais usados. Conforme as necessidades de armazenamento crescem, o StorSimple nivela os dados frios em um armazenamento em nuvem econômico. Os dados têm eliminação de duplicação e são compactados antes de enviar para a nuvem para reduzir ainda mais os requisitos e as despesas de armazenamento. |
| Gerenciamento de armazenamento simplificado |O StorSimple fornece gerenciamento centralizado na nuvem usando o StorSimple Device Manager para gerenciar vários dispositivos. |
| Melhor recuperação de desastres e conformidade |O StorSimple facilita a recuperação de desastres mais rápida restaurando os metadados imediatamente e restaurando os dados conforme necessário. Isso significa que as operações normais podem continuar com o mínimo de interrupção. |
| Mobilidade de dados |Os dados em camadas para a nuvem podem ser acessados de outros sites para fins de recuperação e migração. Observe que você pode restaurar dados somente para a matriz virtual original. No entanto, você usa recursos de recuperação de desastre para restaurar a matriz virtual inteira para outra matriz virtual. |

## <a name="storsimple-workload-summary"></a>Resumo da carga de trabalho do StorSimple

Um resumo das cargas de trabalho do StorSimple com suporte é tabulado abaixo.

|Cenário     |Carga de trabalho     |Suportadas      |Restrições               | Versões aplicáveis|
|-------------|-------------|---------------|---------------------------|--------------------|
|Escritório remoto/filial (ROBO)  |Compartilhamento de arquivos     |Sim      |Consulte [limites máximos para o servidor de arquivos](storsimple-ova-limits.md).<br></br>Consulte [requisitos de sistema para versões SMB com suporte](storsimple-ova-system-requirements.md).| Todas as versões     |
|Arquivamento em nuvem  |Compartilhamento de arquivos de arquivamento     |Sim      |Consulte [limites máximos para o servidor de arquivos](storsimple-ova-limits.md).<br></br>Consulte [requisitos de sistema para versões SMB com suporte](storsimple-ova-system-requirements.md).| Todas as versões     |

A matriz virtual StorSimple é mais adequada para dados acessados com pouca frequência. Embora a matriz virtual tenha um cache local para impulsionar o desempenho, os usuários devem assumir que os arquivos de serviços de dispositivo na camada mais baixa de armazenamento (a nuvem). Cada matriz virtual pode gravar e ler no armazenamento do Azure em aproximadamente 100 Mbps. Esse link é compartilhado entre todas as solicitações que chegam ao dispositivo e pode se tornar um afunilamento, conforme mostrado no diagrama a seguir.

![Arquivamento em nuvem](./media/storsimple-ova-overview/cloud-archiving.png)

Quando vários usuários simultâneos acessam a matriz virtual, todos eles compartilham a conexão com o Azure, levando a um desempenho mais baixo. Não há desempenho garantido por usuário e o dispositivo processa solicitações individuais à medida que elas chegam.

O StorSimple virtual array não é adequado para cargas de trabalho que exigem alta disponibilidade. A matriz virtual é um dispositivo de nó único que apresenta tempo de inatividade quando as atualizações de software são instaladas. Os administradores devem planejar uma janela de manutenção de 30 minutos e 3-4 vezes por ano.

## <a name="workflows"></a>Fluxos de trabalho

A matriz virtual StorSimple é especialmente adequada para os seguintes fluxos de trabalho:

* [Gerenciamento de armazenamento baseado em nuvem](#cloud-based-storage-management)
* [Backup independente de local](#location-independent-backup)
* [Proteção de dados e recuperação de desastre](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Gerenciamento de armazenamento baseado em nuvem
Você pode usar o serviço StorSimple Device Manager em execução no portal do Azure para gerenciar os dados armazenados em vários dispositivos e em vários locais. Isso é particularmente útil em cenários de Branch distribuído. Observe que você deve criar instâncias separadas do serviço de Device Manager do StorSimple para gerenciar matrizes virtuais e dispositivos físicos do StorSimple. Observe também que a matriz virtual agora usa o novo portal do Azure em vez do portal clássico do Azure.

![gerenciamento de armazenamento baseado em nuvem](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Backup independente de local
Com a matriz virtual, os instantâneos de nuvem fornecem uma cópia point-in-time independente de local de um volume ou compartilhamento. Os instantâneos de nuvem são habilitados por padrão e não podem ser desabilitados. Todos os volumes e compartilhamentos são backups ao mesmo tempo por meio de uma única política de backup diário, e você pode fazer backups ad hoc adicionais sempre que necessário.

### <a name="data-protection-and-disaster-recovery"></a>Proteção de dados e recuperação de desastre
A matriz virtual dá suporte aos seguintes cenários de proteção de dados e recuperação de desastre:

* **Restauração de volume ou compartilhamento** – use o fluxo de trabalho restaurar como novo para recuperar um volume ou compartilhamento. Use essa abordagem para recuperar o volume ou o compartilhamento inteiro.
* **Recuperação em nível de item** – os compartilhamentos permitem acesso simplificado a backups recentes. Você pode recuperar facilmente um arquivo individual de uma pasta *. backup* especial disponível na nuvem. Esse recurso de restauração é controlado pelo usuário e nenhuma intervenção administrativa é necessária.
* **Recuperação de desastres** – use o recurso de failover para recuperar todos os volumes ou compartilhamentos para uma nova matriz virtual. Você cria a nova matriz virtual e a registra com o serviço de Device Manager do StorSimple e, em seguida, faz o failover da matriz virtual original. A nova matriz virtual assumirá então os recursos provisionados.

## <a name="storsimple-virtual-array-components"></a>Componentes da matriz virtual StorSimple

A matriz virtual inclui os seguintes componentes:

* [Matriz virtual](#virtual-array) – um dispositivo de armazenamento de nuvem híbrida baseado em uma máquina virtual provisionada em seu ambiente virtualizado ou hipervisor.
* [Serviço Device Manager do StorSimple](#storsimple-device-manager-service) – uma extensão do portal do Azure que permite que você gerencie um ou mais dispositivos storsimple de uma única interface da Web que você pode acessar de diferentes localizações geográficas. Você pode usar o serviço de Device Manager do StorSimple para criar e gerenciar serviços, exibir e gerenciar dispositivos e alertas e gerenciar volumes, compartilhamentos e instantâneos existentes.
* [Interface do usuário da Web local](#local-web-user-interface) – uma IU baseada na Web que é usada para configurar o dispositivo para que ele possa se conectar à rede local e, em seguida, registrar o dispositivo com o serviço StorSimple Device Manager. 
* [Interface de linha de comando](#command-line-interface) – uma interface do Windows PowerShell que você pode usar para iniciar uma sessão de suporte na matriz virtual.
  As seções a seguir descrevem cada um desses componentes com mais detalhes e explicam como a solução organiza os dados, aloca o armazenamento e facilita o gerenciamento de armazenamento e a proteção de dados.

### <a name="virtual-array"></a>Matriz virtual

A matriz virtual é uma solução de armazenamento de nó único que fornece armazenamento primário, gerencia a comunicação com o armazenamento em nuvem e ajuda a garantir a segurança e a confidencialidade de todos os dados armazenados no dispositivo.

A matriz virtual está disponível em um modelo que está disponível para download. A matriz virtual tem uma capacidade máxima de 6,4 TB no dispositivo (com um requisito de armazenamento subjacente de 8 TB) e 64 TB, incluindo o armazenamento em nuvem.

A matriz virtual tem os seguintes recursos:

* Ele é econômico. Ele faz uso de sua infraestrutura de virtualização existente e pode ser implantado em seu hipervisor Hyper-V ou VMware existente.
* Ele reside no datacenter e pode ser configurado como um servidor iSCSI ou um servidor de arquivos.
* Ele é integrado à nuvem.
* Os backups são armazenados na nuvem, o que pode facilitar a recuperação de desastres e simplificar a ILR (recuperação em nível de item).
* Você pode aplicar atualizações à matriz virtual, assim como você as aplicaria a um dispositivo físico.

> [!NOTE]
> Uma matriz virtual não pode ser expandida. Portanto, é importante provisionar o armazenamento adequado ao criar a matriz virtual.

### <a name="storsimple-device-manager-service"></a>Serviço de Gestor de Dispositivos do StorSimple

O Microsoft Azure StorSimple fornece uma interface do usuário baseada na Web, o serviço StorSimple Device Manager, que permite que você gerencie centralmente o armazenamento do StorSimple. Você pode usar o serviço de Device Manager do StorSimple para executar as seguintes tarefas:

* Gerencie várias matrizes virtuais do StorSimple de um único serviço.
* Configurar e gerenciar configurações de segurança para matrizes virtuais StorSimple. (A criptografia na nuvem depende de Microsoft Azure APIs.)
* Configurar credenciais e propriedades da conta de armazenamento.
* Configurar e gerenciar volumes ou compartilhamentos.
* Fazer backup e restaurar dados em volumes ou compartilhamentos.
* Monitore o desempenho.
* Examine as configurações do sistema e identifique possíveis problemas.

Você usa o serviço de Device Manager do StorSimple para executar a administração diária de sua matriz virtual.

Para obter mais informações, acesse [usar o serviço storsimple Device Manager para administrar seu dispositivo storsimple](storsimple-virtual-array-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Interface do usuário da Web local

A matriz virtual inclui uma interface do usuário baseada na Web que é usada para a configuração única e o registro do dispositivo com o serviço StorSimple Device Manager. Você pode usá-lo para desligar e reiniciar a matriz virtual, executar testes de diagnóstico, atualizar software, alterar a senha de administrador do dispositivo, exibir logs do sistema e contatar Suporte da Microsoft para arquivar uma solicitação de serviço.

Para obter informações sobre como usar a interface do usuário baseada na Web, acesse [usar a interface do usuário baseada na Web para administrar seu StorSimple virtual array](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Interface de linha de comando

A interface do Windows PowerShell incluída permite que você inicie uma sessão de suporte com o Suporte da Microsoft para que eles possam ajudá-lo a solucionar problemas e resolver questões que você pode encontrar em sua matriz virtual.

## <a name="storage-management-technologies"></a>Tecnologias de gerenciamento de armazenamento

Além da matriz virtual e de outros componentes, a solução StorSimple usa as seguintes tecnologias de software para fornecer acesso rápido a dados importantes, reduzir o consumo de armazenamento e proteger os dados armazenados em sua matriz virtual:

* [Camadas de armazenamento automático](#automatic-storage-tiering) 
* [Volumes e compartilhamentos fixados localmente](#locally-pinned-shares-and-volumes)
* Eliminação de duplicação e compactação para dados em camadas ou com backup na nuvem 
* [Backups agendados e sob demanda](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Camadas de armazenamento automático
A matriz virtual usa um novo mecanismo de camadas para gerenciar dados armazenados na matriz virtual e na nuvem. Há apenas duas camadas: a matriz virtual local e o armazenamento em nuvem do Azure. O StorSimple virtual array organiza automaticamente os dados nas camadas com base em um mapa de calor, que controla o uso atual, a idade e as relações com outros dados. Os dados mais ativos (mais interessantes) são armazenados localmente, enquanto os dados menos ativos e inativos são migrados automaticamente para a nuvem. (Todos os backups são armazenados na nuvem.) O StorSimple ajusta e reorganiza as atribuições de dados e armazenamento conforme os padrões de uso mudam. Por exemplo, algumas informações podem se tornar menos ativas ao longo do tempo. Como ele se torna progressivamente menos ativo, ele está em camadas para a nuvem. Se esses mesmos dados se tornarem ativos novamente, ele será colocado em camadas na matriz de armazenamento.

Os dados de um determinado compartilhamento ou volume em camadas têm garantia de seu próprio espaço de camada local (aproximadamente 10% do espaço total provisionado para esse compartilhamento ou volume). Embora isso reduza o armazenamento disponível na matriz virtual para esse compartilhamento ou volume, ele garante que a disposição em camadas de um volume ou compartilhamento não será afetada pelas necessidades de camadas de outros compartilhamentos ou volumes. Portanto, uma carga de trabalho muito ocupada em um compartilhamento ou volume não pode forçar todas as outras cargas de trabalho para a nuvem.

Os volumes em camadas criados para o iSCSI têm uma reserva local máxima de 200 GB, independentemente do tamanho do volume.

![camadas de armazenamento automático](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> Você pode especificar um volume como fixado localmente e, nesse caso, os dados permanecem na matriz virtual e nunca são distribuídos em camadas para a nuvem. Para obter mais informações, vá para [volumes e compartilhamentos localmente afixados](#locally-pinned-shares-and-volumes).


### <a name="locally-pinned-shares-and-volumes"></a>Volumes e compartilhamentos fixados localmente

Você pode criar compartilhamentos e volumes apropriados como fixados localmente. Esse recurso garante que os dados exigidos por aplicativos críticos permaneçam na matriz virtual e nunca sejam distribuídos em camadas para a nuvem. Os volumes e compartilhamentos fixados localmente têm os seguintes recursos:

* Eles não estão sujeitos a problemas de conectividade ou latências de nuvem.
* Eles ainda se beneficiam de recursos de recuperação de desastre e backup na nuvem do StorSimple.

Você pode restaurar um volume ou compartilhamento fixado localmente como em camadas ou em um volume ou compartilhamento em camadas como fixado localmente. 

Para obter mais informações sobre volumes fixados localmente, acesse [usar o serviço StorSimple Device Manager para gerenciar volumes](storsimple-virtual-array-manage-volumes.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Eliminação de duplicação e compactação para dados em camadas ou com backup na nuvem

O StorSimple usa eliminação de duplicação e compactação de dados para reduzir ainda mais os requisitos de armazenamento na nuvem. A eliminação de duplicação reduz a quantidade geral de dados armazenados eliminando a redundância no conjunto de dados armazenado. À medida que as informações são alteradas, o StorSimple ignora os dados inalterados e captura apenas as alterações. Além disso, o StorSimple reduz a quantidade de dados armazenados identificando e removendo informações duplicadas.

> [!NOTE]
> Os dados armazenados na matriz virtual não têm eliminação de duplicação ou compactação. Toda a eliminação de duplicação e a compactação ocorre pouco antes de os dados serem enviados para a nuvem.

### <a name="scheduled-and-on-demand-backups"></a>Backups agendados e sob demanda

Os recursos de proteção de dados do StorSimple permitem que você crie backups sob demanda. Além disso, um agendamento de backup padrão garante que o backup dos dados seja feito diariamente. Os backups são feitos na forma de instantâneos incrementais, que são armazenados na nuvem. Os instantâneos, que registram apenas as alterações desde o último backup, podem ser criados e restaurados rapidamente. Esses instantâneos podem ser extremamente importantes em cenários de recuperação de desastres, pois substituem os sistemas de armazenamento secundários (como backup em fita) e permitem que você restaure dados para seu datacenter ou para sites alternativos, se necessário.

## <a name="managing-personal-information"></a>Gerenciando informações pessoais

O StorSimple Device Manager para série virtual coleta informações pessoais em duas instâncias de chave:
 - Alerta as configurações de usuário em que os endereços de email dos usuários estão configurados. Essas informações podem ser limpas pelo administrador. 
 - Usuários que podem acessar os dados que residem nos compartilhamentos. Uma lista de usuários que podem acessar os dados de compartilhamento é exibida e pode ser exportada. Essa lista também é excluída quando os compartilhamentos são excluídos.

Para obter mais informações, consulte a [política de privacidade da Microsoft na central de confiabilidade](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Passos seguintes

Saiba como [preparar o portal da matriz virtual](storsimple-virtual-array-deploy1-portal-prep.md).
