---
title: Monitorização de ameaças no armazenamento do Azure
description: Configure o Azure Storage proteção avançada contra ameaças para detetar anomalias na atividade de conta e notificá-lo de tentativas prejudiciais de aceder à sua conta.
services: storage
author: rmatchoro
ms.service: storage
ms.topic: article
ms.date: 09/24/2018
ms.author: ronmat
ms.manager: shaik
ms.openlocfilehash: 00de38aa7309179b92ff65f009f8aa780f60c284
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56883696"
---
# <a name="azure-storage-advanced-threat-protection"></a>Proteção avançada contra ameaças de armazenamento do Azure

Azure Storage proteção avançada contra ameaças Deteta anomalias na atividade de conta e notifica-o do tentativas prejudiciais de aceder à sua conta. Esta camada de proteção permite-lhe a ameaças de endereço sem a necessidade de ser um especialista em segurança ou gerir sistemas de monitorização de segurança.

Ameaças são apresentadas ao definir alertas de segurança que acionam quando ocorrem anomalias na atividade. Estes alertas integram [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/) que incluem detalhes de atividade suspeita e recomendações sobre como investigar e corrigir as ameaças. 

> [!NOTE]
> Proteção de ameaças de avançada do armazenamento do Azure está atualmente disponível apenas para o serviço de Blobs. Alertas de segurança são integrados no Centro de segurança do Azure e são enviados por e-mail aos administradores de subscrição.

Proteção de ameaças de avançada do armazenamento do Azure ingere os registos de diagnóstico de ler, escrever e eliminar pedidos ao serviço de BLOBs para deteção de ameaças. Para investigar os alertas de proteção avançada contra ameaças, precisa [configurar os registos de diagnóstico](storage-monitor-storage-account.md#configure-logging) para permitir que todos os níveis de registos para o serviço de Blobs.

## <a name="set-up-advanced-threat-protection-in-the-portal"></a>Configurar a proteção avançada contra ameaças no portal

1. Iniciar o portal do Azure no [ https://portal.azure.com ](https://portal.azure.com/).

2. Navegue para a página de configuração da conta de armazenamento do Azure que pretende proteger. Na **configurações** página, selecione **proteção avançada contra ameaças**.

3. Na **proteção avançada contra ameaças** painel de configuração
    * Ative **ON** Advanced *proteção contra ameaças*
    * Clique em **guardar** para guardar a política de proteção avançada contra ameaças nova ou atualizada.

![Ativar o armazenamento do Azure, proteção avançada contra ameaças](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

## <a name="explore-anomalies"></a>Explore anomalias

Quando ocorrem de anomalias de atividade de armazenamento, receberá uma notificação por e-mail com informações sobre o evento de segurança suspeitas. Detalhes do evento incluem:

* natureza da anomalia
* Nome da conta de armazenamento
* Tipo de armazenamento
* Hora do evento

O e-mail também inclui detalhes sobre as causas possíveis e as ações recomendadas para investigar e mitigar a potencial ameaça.

![Armazenamento do Azure advanced e-mail de alerta de proteção de ameaças](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Pode rever e gerir os alertas de segurança atual a partir do Centro de segurança do Azure [mosaico alertas de segurança](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts). Clicar num alerta específico fornece detalhes e as ações para investigar a ameaça atual e abordar ameaças futuras.

![Armazenamento do Azure advanced e-mail de alerta de proteção de ameaças](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Alertas de proteção

Os alertas são gerados pelas tentativas invulgares e potencialmente prejudiciais de aceder ou explorar as contas de armazenamento. Esses eventos podem disparar os seguintes alertas:

* **Acesso a partir de uma localização invulgar**: Este alerta é acionado quando ocorre uma alteração no padrão de acesso a uma conta de armazenamento. Por exemplo, quando alguém acedeu uma conta de armazenamento de uma localização geográfica invulgar. Em alguns casos, o alerta Deteta uma ação legítima (uma nova aplicação ou operação de manutenção do desenvolvedor). Em outros casos, o alerta Deteta uma ação maliciosa (ex-funcionário, atacante externo, etc.).

* **Extração de dados incomuns**: Este alerta é acionado quando ocorre uma alteração no padrão de extração de dados de uma conta de armazenamento. Por exemplo, se alguém acedeu uma quantidade invulgar de dados numa conta de armazenamento. Em alguns casos, o alerta Deteta uma ação legítima (atividade de manutenção). Em outros casos, o alerta Deteta uma ação maliciosa (filtragem/violação de dados, não autorizada de transferência de dados).

* **Acesso invulgar a anónimo:** Este alerta é acionado quando ocorre uma alteração no padrão de acesso a uma conta de armazenamento. Por exemplo, suponha que alguém tenha acedido anonimamente a uma conta de armazenamento. Em alguns casos, o alerta Deteta um acesso legítimo com acesso de leitura público. Em outros casos, o alerta Deteta o acesso não autorizado que explore o acesso de leitura público para um contentor e respetivos blobs.

* **Eliminar inesperado:** Este alerta é acionado quando uma ou mais operações de eliminação inesperado ocorrem numa conta de armazenamento, com base na análise de histórico da conta de armazenamento. Por exemplo, suponha que alguém efetuar uma *DeleteBlob* operação usando uma nova aplicação e para um novo endereço IP. Em alguns casos, o alerta Deteta uma ação legítima (o administrador utilizado um browser diferente numa viagem de negócios). Em outros casos, o alerta Deteta uma ação maliciosa (um atacante a eliminação de dados). 
 
* **Alteração de permissão de acesso:** Este alerta é acionado quando ocorre uma alteração inesperada de permissão de acesso a uma conta de armazenamento. Por exemplo, suponha que alguém altera a permissão de acesso de uma conta de armazenamento com uma nova aplicação e para um novo endereço IP. Em alguns casos, o alerta Deteta uma ação legítima (o administrador utilizado um browser diferente numa viagem de negócios). Em outros casos, o alerta Deteta uma ação maliciosa (por exemplo, um invasor aumentar os privilégios de uma conta que tenham obtido acesso ao). 

* **Carregar o pacote de serviço Cloud do Azure:** Este alerta é acionado quando existe um carregamento inesperado de um pacote de serviço Cloud do Azure (*cspkg* ficheiro) para uma conta de armazenamento. Por exemplo, suponha que um *cspkg* ficheiro foi carregado a partir de um novo endereço IP. Em alguns casos, o alerta Deteta uma ação legítima. Em outros casos, o alerta Deteta uma ação maliciosa (por exemplo, um serviço em nuvem pacote foi carregado na preparação de uma implantação de um serviço mal-intencionado).    
   

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [registos nas contas de armazenamento do Azure](/rest/api/storageservices/About-Storage-Analytics-Logging)

* Saiba mais sobre [Centro de segurança do Azure](../../security-center/security-center-intro.md)