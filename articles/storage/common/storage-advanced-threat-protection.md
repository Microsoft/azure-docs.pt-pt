---
title: Proteção avançada contra ameaças para o armazenamento do Azure
description: Configure o Azure Storage proteção avançada contra ameaças para detetar anomalias na atividade de conta e notificá-lo de tentativas prejudiciais de aceder à sua conta.
services: storage
author: rmatchoro
ms.service: storage
ms.topic: article
ms.date: 03/14/2019
ms.author: monhaber
ms.manager: shaik
ms.openlocfilehash: b2e51b11e2d79d7f35d6b24df4ba5492ecf9a5c4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58133234"
---
# <a name="advanced-threat-protection-for-azure-storage"></a>Proteção avançada contra ameaças para o armazenamento do Azure

Proteção avançada contra ameaças do armazenamento do Azure fornece uma camada adicional de inteligência de segurança Deteta tentativas invulgares e potencialmente prejudiciais de aceder ou explorar as contas de armazenamento. Esta camada de proteção permite-lhe a ameaças de endereço sem a necessidade de ser um especialista em segurança ou gerir sistemas de monitorização de segurança. 

Alertas de segurança são acionados quando ocorrerem de anomalias na atividade.  Estes alertas de segurança estão integradas [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/)e também são enviados por e-mail aos administradores de subscrição, com detalhes de atividade suspeita e recomendações sobre como investigar e corrigir as ameaças.

> [!NOTE]
> * Proteção avançada contra ameaças do armazenamento do Azure está atualmente disponível apenas para o armazenamento de Blobs. 
> * Os detalhes sobre os novos preços estão disponíveis no [Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-pricing) página, incluindo uma opção para um período de avaliação para os primeiros 30 dias sem custos de preços. 
> * ATP para a funcionalidade de armazenamento do Azure não está atualmente disponível nas regiões de cloud soberana e do Azure government.

Proteção avançada contra ameaças do armazenamento do Azure ingere os registos de diagnóstico de leitura, escrita e pedidos de eliminação para o armazenamento de BLOBs para a deteção de ameaças. Para investigar os alertas de proteção avançada contra ameaças, pode ver a atividade de armazenamento relacionados com o registo de análise de armazenamento. Para obter mais informações, consulte como [configurar o registo de análise de armazenamento](storage-monitor-storage-account.md#configure-logging).

## <a name="set-up-advanced-threat-protection"></a>Configurar a proteção avançada contra ameaças 

### <a name="using-the-portal"></a>Utilizar o portal

1. Iniciar o portal do Azure no [ https://portal.azure.com ](https://portal.azure.com/).

2. Navegue para a página de configuração da conta de armazenamento do Azure que pretende proteger. Na **configurações** página, selecione **proteção avançada contra ameaças**.

3. Na **proteção avançada contra ameaças** painel de configuração
    * Ative **ON** Advanced *proteção contra ameaças*
    * Clique em **guardar** para guardar a política de proteção avançada contra ameaças nova ou atualizada. (Os preços na imagem são por exemplo apenas para fins.)

![Ativar o armazenamento do Azure, proteção avançada contra ameaças](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="using-azure-security-center"></a>O Centro de segurança do Azure
Quando subscreve o escalão Standard no Centro de segurança do Azure, proteção avançada contra ameaças está configurada na sua conta de armazenamento. Para obter mais informações, consulte [atualizar para o escalão Standard do Centro de segurança para uma maior segurança](https://docs.microsoft.com/azure/security-center/security-center-pricing). (Os preços na imagem são por exemplo apenas para fins.)

![Escalão Standard no ASC](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing.png)

### <a name="using-azure-resource-manager-templates"></a>Utilizando modelos Azure Resource Manager

Utilize um modelo do Azure Resource Manager para implementar uma conta de armazenamento do Azure com proteção avançada contra ameaças ativada.
Para obter mais informações, consulte [conta de armazenamento com proteção avançada contra ameaças](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="using-rest-api"></a>Com a API REST
Utilize comandos de Rest API para criar, atualizar ou obter a definição de proteção avançada contra ameaças para uma conta de armazenamento específico.

* [Proteção avançada contra ameaças - criar](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Proteção avançada contra ameaças - introdução](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell

Utilize os seguintes cmdlets do PowerShell:

  * [Ativar a proteção avançada contra ameaças](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
  * [Obter proteção avançada contra ameaças](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
  * [Desative a proteção avançada contra ameaças](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

## <a name="explore-security-anomalies"></a>Explore anomalias de segurança

Quando ocorrem de anomalias de atividade de armazenamento, receberá uma notificação por e-mail com informações sobre o evento de segurança suspeitas. Detalhes do evento incluem:

* A natureza da anomalia
* O nome da conta de armazenamento
* A hora do evento
* O tipo de armazenamento
* As causas possíveis 
* Os passos de investigação
* Os passos de remediação


O e-mail também inclui detalhes sobre as causas possíveis e as ações recomendadas para investigar e mitigar a potencial ameaça.

![Armazenamento do Azure advanced e-mail de alerta de proteção de ameaças](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Pode rever e gerir os alertas de segurança atual a partir do Centro de segurança do Azure [mosaico alertas de segurança](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts). Clicar num alerta específico fornece detalhes e as ações para investigar a ameaça atual e abordar ameaças futuras.

![Armazenamento do Azure advanced e-mail de alerta de proteção de ameaças](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Alertas de proteção

Os alertas são gerados pelas tentativas invulgares e potencialmente prejudiciais de aceder ou explorar as contas de armazenamento. Esses eventos podem disparar os seguintes alertas:

### <a name="anomalous-access-pattern-alerts"></a>Alertas de padrão de acesso anómalos

* **Acesso a partir de uma localização invulgar**: Este alerta é acionado quando ocorre uma alteração no padrão de acesso a uma conta de armazenamento. Por exemplo, quando alguém acedeu uma conta de armazenamento de uma localização geográfica invulgar.
Causas possíveis:
   * Um atacante acedeu a sua conta de armazenamento
   * Um usuário legítimo acedeu a sua conta de armazenamento de uma nova localização
 
* **Anomalias de aplicativo**: Este alerta indica que um aplicativo fora do comum acedeu esta conta de armazenamento. Causas possíveis:
   * Um atacante acedeu a sua conta de armazenamento através de uma nova aplicação.
   * Um usuário legítimo usou um novo aplicativo/browser para aceder à sua conta de armazenamento.

* **Acesso anónimo**: Este alerta indica que existe uma alteração no padrão de acesso a uma conta de armazenamento. Por exemplo, esta conta tem sido acedidos anonimamente (ou seja, sem qualquer autenticação), que é inesperado em comparação com o padrão de acesso recentes nesta conta.
Causas possíveis:
   * Um invasor ter explorado acesso de leitura público para um contentor.
   * Um usuário legítimo ou aplicativo tenha utilizado o acesso de leitura público para um contentor.

### <a name="anomalous-extractupload-alerts"></a>Alertas de carregamento/extração anómalos

* **Filtragem de dados**: Este alerta indica que foi extraída uma excecionalmente grande quantidade de dados em comparação com a atividade recente deste contentor de armazenamento. Causas possíveis:
   * Um invasor tem extrair uma grande quantidade de dados de um contentor. (Por exemplo: filtragem/violação de dados, a transferência não autorizada de dados)
   * Um usuário legítimo ou a aplicação tem extrair uma invulgar quantidade de dados de um contentor. (Por exemplo: atividade de manutenção)

* **Eliminar inesperado**: Este alerta indica que uma ou mais operações de eliminação inesperado ocorreu numa conta de armazenamento, em comparação com a atividade recente nesta conta. Causas possíveis:
   * Um invasor eliminou dados da sua conta de armazenamento.
   * Um usuário legítimo tem de efetuar uma eliminação invulgar.

* **Carregar o pacote de serviço Cloud do Azure**: Este alerta indica que um pacote de serviço Cloud do Azure (ficheiro. cspkg) foi carregado para uma conta de armazenamento de forma invulgar, em comparação comparada a atividade recente nesta conta. Causas possíveis: 
   * Um invasor tem sido Preparando para implantar o código malicioso da sua conta de armazenamento a um serviço cloud do Azure.
   * Um usuário legítimo tem sido a preparar a implementação de serviço legítimos.

### <a name="suspicious-storage-activities-alerts"></a>Alertas de atividades suspeitas de armazenamento

* **Alteração de permissão de acesso**: Este alerta indica que as permissões de acesso desse contêiner de armazenamento foram alteradas de forma invulgar. Causas possíveis: 
   * Um invasor foi alterado as permissões de contentor enfraquecer a segurança.
   * Um usuário legítimo foi alterado as permissões de contentor.

* **Aceder a inspeção**: Este alerta indica que as permissões de acesso das contas de armazenamento tem sido inspecioná-los de forma invulgar, em comparação comparada a atividade recente nesta conta. Causas possíveis: 
   * Um atacante efetuou o reconhecimento de um ataque de futuro.
   * Um usuário legítimo realizou a manutenção da conta de armazenamento.

* **Exploração de dados**: Este alerta indica que tiverem sido enumerados blobs ou contentores numa conta de armazenamento de forma invulgar, em comparação comparada a atividade recente nesta conta. Causas possíveis: 
   * Um atacante efetuou o reconhecimento de um ataque de futuro.
   * Um usuário legítimo ou a lógica do aplicativo tiver explorado dados na conta de armazenamento.






## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [registos nas contas de armazenamento do Azure](/rest/api/storageservices/About-Storage-Analytics-Logging)

* Saiba mais sobre [Centro de segurança do Azure](../../security-center/security-center-intro.md)