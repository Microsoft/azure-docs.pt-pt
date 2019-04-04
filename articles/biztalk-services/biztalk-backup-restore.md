---
title: Criar e restaurar uma cópia de segurança dos serviços BizTalk | Documentos da Microsoft
description: Os serviços BizTalk inclui a cópia de segurança e restauro. Saiba como criar e restaurar uma cópia de segurança e determinar o que obtém uma cópia de segurança. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 59f91173-4683-48df-abd5-41262bfce6df
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: ee86b9aa2d920668cf036f3e8f8634e9289e8913
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916874"
---
# <a name="biztalk-services-backup-and-restore"></a>Serviços BizTalk: Cópia de Segurança e Restauro

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Serviços BizTalk do Azure inclui capacidades de cópia de segurança e restauro. 

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

> [!NOTE]
> Ligações híbridas não são uma cópia de segurança, independentemente da edição. Tem de recriar as ligações híbridas.


## <a name="before-you-begin"></a>Antes de começar
* Cópia de segurança e restauro podem não estar disponível para todas as edições. Consulte [os serviços BizTalk: Gráfico de edições](biztalk-editions-feature-chart.md).
* Pode ser restaurado o conteúdo de cópia de segurança para o mesmo serviço BizTalk ou para um novo BizTalk Service. Para restaurar o serviço BizTalk utilizando o mesmo nome, o serviço BizTalk existentes têm de ser eliminado e o nome tem de estar disponível. Depois de eliminar um BizTalk Service, pode demorar mais tempo do que pretendia para o mesmo nome para estar disponível. Se não pode esperar para o mesmo nome para estar disponível, em seguida, restaure para um novo BizTalk Service.
* Os serviços do BizTalk podem ser restaurados para a mesma edição ou uma edição superior. Restaurar os serviços BizTalk para uma edição mais baixa, de quando foi feita a cópia de segurança, não é suportada.
  
    Por exemplo, uma cópia de segurança através da edição básica pode ser restaurada para a edição Premium. Não é possível restaurar uma cópia de segurança através da edição de Premium para a edição Standard.
* Os números de controlo de EDI são uma cópia de segurança para manter a continuidade dos números de controlo. Se as mensagens são processadas após a última cópia de segurança, restaurar este conteúdo de cópia de segurança pode fazer com que os números de controlo duplicado.
* Se um lote tiver mensagens ativas, processar o batch **antes de** executar uma cópia de segurança. Ao criar uma cópia de segurança (como necessário ou agendadas), mensagens em lotes nunca são armazenadas. 
  
    **Se uma cópia de segurança está a ser utilizada com mensagens ativas num batch, estas mensagens não são uma cópia de segurança e, portanto, são perdidas.**
* Opcional: No Portal de serviços do BizTalk, pare quaisquer operações de gestão.

## <a name="create-a-backup"></a>Criar uma cópia de segurança
Uma cópia de segurança pode ser executada em qualquer altura e é totalmente controlada por si. Para criar uma cópia de segurança, utilize o [API REST para gerir os serviços BizTalk no Azure](/previous-versions/azure/reference/dn232347(v=azure.100)).

## <a name="restore"></a>Restauro
Para restaurar uma cópia de segurança, utilize o [API REST para gerir os serviços BizTalk no Azure](/previous-versions/azure/reference/dn232347(v=azure.100)).

### <a name="postrestore"></a>Depois de restaurar uma cópia de segurança
O serviço BizTalk sempre será restaurado numa **suspenso** estado. Neste estado, pode fazer alterações de configuração antes do novo ambiente é funcional, incluindo:

* Se tiver criado a aplicações do BizTalk Service com o SDK de serviços BizTalk do Azure, terá de atualizar as credenciais de controlo de acesso (ACS) nesses aplicativos para que funcionem com o ambiente restaurado.
* Restaurar um BizTalk Service para replicar um ambiente existente do BizTalk Service. Nesta situação, se existirem contratos configurados no portal dos serviços BizTalk do original que utilizam uma pasta de FTP de origem, terá de atualizar os contratos no ambiente de recentemente restaurado ao utilizar uma pasta FTP de origem diferente. Caso contrário, pode haver dois contratos diferentes tentando puxar a mesma mensagem.
* Se os tiver restaurado ter vários ambientes de serviço BizTalk, certifique-se de que o ambiente correto na aplicativos do Visual Studio, cmdlets do PowerShell, REST APIs ou APIs de OM do gestão de parceiros comerciais de destino.
* É uma boa prática para configurar cópias de segurança automáticas no ambiente de serviço BizTalk recentemente restaurado.

## <a name="what-gets-backed-up"></a>O que obtém uma cópia de segurança
Quando é criada uma cópia de segurança, os seguintes itens são uma cópia de segurança:

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>Itens de cópia de segurança</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Portal dos serviços BizTalk do Azure</strong></td>
</tr> 
<tr>
<td>Configuração e o tempo de execução</td> 
<td>
<ul>
<li>Detalhes de perfil e de parceiros</li>
<li>Contratos de parceiros</li>
<li>Assemblies personalizados, implementados</li>
<li>Pontes implementados</li>
<li>Certificados</li>
<li>Transformações implementadas</li>
<li>Pipelines</li>
<li>Modelos que criou e guardou no Portal de serviços do BizTalk</li>
<li>X12 mapeamentos ST01 e GS01</li>
<li>Números de controlo (EDI)</li>
<li>Valores do MIC de mensagem AS2</li>
</ul>
</td>
</tr> 

<tr>
<td colspan="2">
 <strong>Serviço BizTalk do Azure</strong></td>
</tr> 
<tr>
<td>Certificado SSL</td> 
<td>
<ul>
<li>Dados de certificado SSL</li>
<li>Palavra-passe de certificado SSL</li>
</ul>
</td>
</tr> 
<tr>
<td>Definições do BizTalk Service</td> 
<td>
<ul>
<li>Contagem de unidades de escala</li>
<li>Edição</li>
<li>Versão do Produto</li>
<li>Região/Datacenter</li>
<li>O espaço de nomes do Access Control Service (ACS) e a chave</li>
<li>Cadeia de ligação de base de dados de controlo</li>
<li>Arquivamento de cadeia de ligação de conta de armazenamento</li>
<li>Cadeia de ligação de conta de armazenamento de monitorização</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>Itens adicionais</strong></td>
</tr> 
<tr>
<td>Base de dados de controlo</td> 
<td>Quando o serviço BizTalk é criado, os detalhes de base de dados de controlo forem inseridos, incluindo o servidor de base de dados SQL do Azure e o nome de base de dados de controlo. A base de dados de controlo não é automaticamente uma cópia de segurança.
<br/><br/>
<strong>Importante</strong><br/>
Se a base de dados de controlo é eliminado e as necessidades de base de dados recuperada, tem de existir uma cópia de segurança anterior. Se não existir uma cópia de segurança, a base de dados de controlo e os respetivos dados não são recuperáveis. Nesta situação, crie uma nova base de dados de controlo com o mesmo nome de base de dados. Recomenda-se a Georreplicação.</td>
</tr> 
</table>

## <a name="next"></a>Seguinte
Para criar serviços BizTalk do Azure, aceda a [os serviços BizTalk: Aprovisionamento](https://go.microsoft.com/fwlink/p/?LinkID=302280). Para começar a criar aplicações, veja [BizTalk Services do Azure](https://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Consultar Também
* [Serviço de cópia de segurança do BizTalk](https://go.microsoft.com/fwlink/p/?LinkID=325584)
* [Restaurar o serviço BizTalk a partir de cópia de segurança](https://go.microsoft.com/fwlink/p/?LinkID=325582)
* [Serviços BizTalk: Programador, básico, Standard e Premium gráfico de edições](https://go.microsoft.com/fwlink/p/?LinkID=302279)
* [Serviços BizTalk: Aprovisionamento](https://go.microsoft.com/fwlink/p/?LinkID=302280)
* [Serviços BizTalk: Gráfico de estado de aprovisionamento](https://go.microsoft.com/fwlink/p/?LinkID=329870)
* [Serviços BizTalk: Separadores dashboard, monitorizar e dimensionar](https://go.microsoft.com/fwlink/p/?LinkID=302281)
* [Serviços BizTalk: Limitação](https://go.microsoft.com/fwlink/p/?LinkID=302282)
* [Serviços BizTalk: Nome do emissor e chave do emissor](https://go.microsoft.com/fwlink/p/?LinkID=303941)
* [Como posso começar a utilizar o SDK dos BizTalk Services do Azure](https://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png

