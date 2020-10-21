---
title: Relatórios de atividade de início de sessão no portal do Azure Active Directory | Microsoft Docs
description: Introdução aos relatórios de atividade de início de sessão no portal do Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/24/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23074dcd7ee888d6d9d8bf288cf0fda66dc0ceec
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92319762"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Relatórios de atividade de início de sessão no portal do Azure Active Directory

A arquitetura de reporte no Azure Ative Directory (Azure AD) consiste nos seguintes componentes:

- **Atividade** 
    - **Insuposições** – Informações sobre o uso de aplicações geridas e atividades de inscrição do utilizador.
    - **Registos de**  -  auditoria [Os registos de auditoria](concept-audit-logs.md) fornecem informações sobre a atividade do sistema sobre utilizadores e gestão de grupos, aplicações geridas e atividades de diretório.
    - Registos de **provisionamento**  -  [O fornecimento de registos](./concept-provisioning-logs.md) permite que os clientes monitorizem a atividade através do serviço de fornecimento, como a criação de um grupo no ServiceNow ou um utilizador importado do Workday. 
- **Segurança** 
    - **Entradas arriscadas** - Um [sinal de risco](../identity-protection/overview-identity-protection.md) é um indicador para uma tentativa de inscrição por alguém que não é o legítimo proprietário de uma conta de utilizador.
    - **Utilizadores sinalizados para o risco** - Um [utilizador de risco](../identity-protection/overview-identity-protection.md) é um indicador para uma conta de utilizador que pode ter sido comprometida.

Este artigo dá-lhe uma visão geral do relatório de inscrições.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="who-can-access-the-data"></a>Quem pode aceder aos dados?

* Utilizadores nas funções de Administrador de Segurança, Leitor de Segurança, Leitor Global e Leitor de Relatórios
* Administradores Globais
* Qualquer utilizador (não administrador) pode aceder aos seus próprios inícios de sessão 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Que licença do Azure AD precisa para aceder à atividade de entrada?

O relatório de atividades de inscrição está disponível em [todas as edições do AZure AD](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data) e também pode ser acedido através da API do Microsoft Graph.

## <a name="sign-ins-report"></a>Relatório de inscrições

O relatório de inscrições do utilizador fornece respostas às seguintes perguntas:

* O que é o padrão de início de sessão de um utilizador?
* Quantos utilizadores iniciaram sessão ao longo de uma semana?
* Qual é o estado destes inícios de sessão?

No menu do [portal Azure,](https://portal.azure.com) selecione **Azure Ative Directory,** ou procure e selecione **O Diretório Ativo Azure** a partir de qualquer página.

![Selecione Azure Ative Directory](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

Em **Monitorização**, selecione **Iniciar sing-ins** para abrir o [relatório de inscrições](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns).

![Atividade de inscrição](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "Atividade de início de sessão")

Pode levar até duas horas para alguns registos de inscrição aparecerem no portal.

> [!IMPORTANT]
> O relatório de entrada apenas apresenta os insusores **interativos,** isto é, as insusões em que um utilizador assina manualmente a utilização do seu nome de utilizador e palavra-passe. Os insus máximos de inscrição não interativos, como a autenticação de serviço-a-serviço, não são apresentados no relatório de inscrição. 

Um registo de inícios de sessão tem uma vista de listas predefinidas que mostra:

- A data de início de sessão
- O utilizador relacionado
- A aplicação que o utilizador assinou
- O estado de início de sessão
- O estado da deteção de risco
- O estado do requisito de autenticação multifator (MFA)

![Atividade de inscrição](./media/concept-sign-ins/sign-in-activity.png "Atividade de início de sessão")

Pode personalizar a vista de lista ao clicar em **Colunas** na barra de ferramentas.

![Atividade de inscrição](./media/concept-sign-ins/19.png "Atividade de início de sessão")

O diálogo **Colunas** dá-lhe acesso aos atributos selecionáveis. Num relatório de inscrição, não se pode ter campos que tenham mais do que um valor para um dado pedido de inscrição como coluna. Isto é, por exemplo, verdade para detalhes de autenticação, dados de acesso condicional e localização da rede.   

![Atividade de inscrição](./media/concept-sign-ins/columns.png "Atividade de início de sessão")

Selecione um item na vista da lista para obter informações mais detalhadas.

![Atividade de inscrição](./media/concept-sign-ins/basic-sign-in.png "Atividade de início de sessão")

> [!NOTE]
> Os clientes podem agora resolver as políticas de Acesso Condicional através de todos os relatórios de inscrição. Ao clicar no separador **Acesso Condicional** para obter um registo de inscrição, os clientes podem rever o estado de Acesso Condicional e mergulhar nos detalhes das políticas que se aplicam ao registo e o resultado de cada política.
> Para obter mais informações, consulte as [perguntas frequentes sobre informações sobre CA em todos os sign-ins](reports-faq.md#conditional-access).



## <a name="filter-sign-in-activities"></a>Filtrar atividades de início de sessão

Primeiro, reduzindo os dados reportados a um nível que funcione para si. Em segundo lugar, filtrar os dados de inscrição utilizando o campo de datas como filtro predefinido. A Azure AD fornece-lhe uma ampla gama de filtros adicionais que pode definir:

![Atividade de inscrição](./media/concept-sign-ins/04.png "Atividade de início de sessão")

**ID do pedido** - A identificação do pedido que lhe interessa.

**Utilizador** - O nome ou o nome principal do utilizador (UPN) do utilizador de que se preocupa.

**Aplicação** - O nome do pedido-alvo.
 
**Estado** - O estado de inscrição que lhe interessa:

- Success

- Falha

- Interrompido


**Endereço IP** - O endereço IP do dispositivo utilizado para ligar ao seu inquilino.

A **Localização** - A localização da ligação foi iniciada a partir de:

- City

- Estado / Província

- País/Região


**Recurso** - O nome do serviço utilizado para a inscrição.


**ID de recurso** - A identificação do serviço utilizado para a inscrição.


**App cliente** - O tipo de aplicação do cliente usada para ligar ao seu inquilino:

![Filtro de aplicativo de cliente](./media/concept-sign-ins/client-app-filter.png)


|Nome|Autenticação moderna|Descrição|
|---|:-:|---|
|SMTP autenticado| |Usado pelos clientes POP e IMAP para enviar mensagens de correio e-mail.|
|Autodiscover| |Utilizado pelos clientes Outlook e EAS para encontrar e conectar-se a caixas de correio em Exchange Online.|
|Exchange ActiveSync| |Este filtro mostra todas as tentativas de inscrição em que o protocolo EAS foi tentado.|
|Browser|![Marca de verificação azul.](./media/concept-sign-ins/check.png)|Mostra todas as tentativas de inscrição de utilizadores que usam navegadores web|
|Exchange ActiveSync| | Mostra todas as tentativas de login dos utilizadores com aplicações de clientes que usam o Exchange ActiveSync para se conectarem ao Exchange Online|
|Intercâmbio PowerShell Online| |Usado para ligar a Exchange Online com powerShell remoto. Se bloquear a autenticação básica para Exchange Online PowerShell, tem de utilizar o módulo Exchange Online PowerShell para se ligar. Para obter instruções, consulte [Connect to Exchange Online PowerShell utilizando a autenticação de vários fatores.](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)|
|Serviços Web Exchange| |Uma interface de programação que é usada pelo Outlook, Outlook para Mac e aplicações de terceiros.|
|IMAP4| |Um cliente de correio antigo que usa o IMAP para recuperar o e-mail.|
|MAPI sobre HTTP| |Usado pelo Outlook 2010 e mais tarde.|
|Aplicativos móveis e clientes de desktop|![Marca de verificação azul.](./media/concept-sign-ins/check.png)|Mostra todas as tentativas de login dos utilizadores que usam aplicações móveis e clientes de desktop.|
|Livro de endereços offline| |Uma cópia das coleções de listas de endereços que são descarregadas e usadas pelo Outlook.|
|Outlook Anywhere (RPC over HTTP)| |Usado pelo Outlook 2016 e mais cedo.|
|Serviço Outlook| |Utilizado pelo aplicativo Mail and Calendar para o Windows 10.|
|POP3| |Um cliente de correio antigo usando POP3 para recuperar e-mail.|
|Serviços Web de Reporte| |Usado para recuperar dados de relatório no Exchange Online.|
|Outros clientes| |Mostra todas as tentativas de login de utilizadores onde a aplicação do cliente não está incluída ou desconhecida.|



**Sistema operativo** - O sistema operativo em funcionamento do dispositivo usou a inscrição no seu inquilino. 


**Browser do dispositivo** - Se a ligação foi iniciada a partir de um browser, este campo permite-lhe filtrar pelo nome do navegador.


**ID de correlação** - A identificação de correlação da atividade.




**Acesso condicional** - O estado das regras de acesso condicional aplicadas

- **Não aplicada**: Nenhuma política aplicada ao utilizador e à aplicação durante a entrada.

- **Sucesso**: Uma ou mais políticas de acesso condicional aplicadas ao utilizador e à aplicação (mas não necessariamente às outras condições) durante a entrada. 

- **Falha**: A inscrição satisfez o estado de utilização e aplicação de pelo menos uma política de acesso condicional e os controlos de concessão não estão satisfeitos ou definidos para bloquear o acesso.









## <a name="download-sign-in-activities"></a>Transferir atividades de início de sessão

Clique na opção **Download** para criar um ficheiro CSV ou JSON dos registos mais recentes de 250.000. Comece por [fazer o download dos dados de insusição](quickstart-download-sign-in-report.md) se quiser trabalhar com ele fora do portal Azure.  

![Transferência](./media/concept-sign-ins/71.png "Download")

> [!IMPORTANT]
> O número de registos que pode descarregar está limitado pelas políticas de retenção do [relatório Azure Ative Directory](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Atalhos de dados de inscrição

A Azure AD e o portal Azure fornecem-lhe pontos de entrada adicionais para os dados de inscrição:

- A visão geral da proteção de identidade
- Utilizadores
- Grupos
- Aplicações Empresariais

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Utilizadores assinam dados de ins na proteção de segurança de identidade

O gráfico de inscrição do utilizador na página geral de **proteção** de identidade mostra agregações semanais de insinumentos. O padrão para o período de tempo é de 30 dias.

![Atividade de inscrição](./media/concept-sign-ins/06.png "Atividade de início de sessão")

Quando clica num dia no gráfico de início de sessão, obtém uma descrição geral das atividades de início de sessão para este dia.

Cada linha na lista de atividades de início de sessão mostra:

* Quem iniciou sessão?
* Que aplicação foi o destino do início de sessão?
* Qual o estado do início de sessão?
* Qual o estado MFA do início de sessão?

Ao clicar num item, obtém mais detalhes sobre a operação de início de sessão:

- ID de Utilizador
- Utilizador
- Nome de utilizador
- ID da Aplicação
- Aplicação
- Cliente
- Localização
- Endereço IP
- Date
- MFA Necessário
- Estado de início de sessão

> [!NOTE]
> Os endereços IP são emitidos de forma a que não exista uma ligação definitiva entre um endereço IP e onde o computador com esse endereço esteja fisicamente localizado. O mapeamento de endereços IP é complicado pelo facto de os fornecedores móveis e VPNs emitirem endereços IP a partir de piscinas centrais que muitas vezes estão muito longe de onde o dispositivo cliente é realmente usado. Atualmente nos relatórios AD AZure, converter o endereço IP para uma localização física é um melhor esforço com base em vestígios, dados de registo, análises inversas e outras informações.

Na página **Utilizadores**, pode obter uma descrição geral completa de todos os inícios de sessão dos utilizadores ao clicar em **Inícios de sessão** na secção **Atividade**.

![Atividade de inscrição](./media/concept-sign-ins/08.png "Atividade de início de sessão")

## <a name="usage-of-managed-applications"></a>Utilização de aplicações geridas

Com uma vista centrada em aplicações dos seus dados de início de sessão, poderá responder a perguntas como:

* Quem está a utilizar as minhas aplicações?
* Quais são as três principais aplicações na sua organização?
* Como está a minha nova aplicação?

O ponto de entrada nestes dados é o top 3 de aplicações na sua organização. Os dados estão contidos no relatório dos últimos 30 dias na secção **Visão Geral** ao abrigo das **aplicações da Enterprise**.

![Atividade de inscrição](./media/concept-sign-ins/10.png "Atividade de início de sessão")

Os gráficos de utilização da aplicação são agregações semanais de inscrições para as suas três principais aplicações num determinado período de tempo. A predefinição do período de tempo é 30 dias.

![Atividade de inscrição](./media/concept-sign-ins/graph-chart.png "Atividade de início de sessão")

Se quiser, pode colocar o foco numa aplicação específica.

![Relatórios](./media/concept-sign-ins/single-app-usage-graph.png "Relatórios")

Quando clica num dia no gráfico de utilização da aplicação, obtém uma lista detalhada das atividades de início de sessão.

A opção **Inícios de sessão** dá uma visão geral completa de todos os eventos de início de sessão nas suas aplicações.

## <a name="microsoft-365-activity-logs"></a>Registos de atividades da Microsoft 365

Pode ver os registos de atividade do Microsoft 365 a partir do [centro de administração Microsoft 365](/office365/admin/admin-overview/about-the-admin-center). Considere o ponto que, a atividade da Microsoft 365 e os registos de atividade azure AD partilham um número significativo de recursos do diretório. Apenas o centro de administração Microsoft 365 fornece uma visão completa dos registos de atividades da Microsoft 365. 

Também pode aceder aos registos de atividades do Microsoft 365 programáticamente utilizando as [APIs de Gestão do Office 365](/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Passos seguintes

* [Códigos de erro do relatório de atividade de inscrição](reference-sign-ins-error-codes.md)
* [Políticas de retenção de dados Azure AD](reference-reports-data-retention.md)
* [Azure AD reporta latências](reference-reports-latencies.md)