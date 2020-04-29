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
ms.openlocfilehash: 2910933e2c57a8bc80a220726462b02915c4a8eb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80246522"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Relatórios de atividade de início de sessão no portal do Azure Active Directory

A arquitetura de reporte em Azure Ative Directory (Azure AD) consiste nos seguintes componentes:

- **Atividade** 
    - **Iniciars inscrições** – Informação sobre a utilização de aplicações geridas e atividades de sessão de utilizadores.
    - **Registos de auditoria Registos** - [de auditoria Registos](concept-audit-logs.md) de auditoria fornecem informações sobre a atividade do sistema sobre utilizadores e gestão de grupos, aplicações geridas e atividades de diretório.
- **Segurança** 
    - **Inscrições arriscadas** - Um [sinal de inscrição arriscado](concept-risky-sign-ins.md) é um indicador para uma tentativa de inscrição por alguém que não é o legítimo proprietário de uma conta de utilizador.
    - **Utilizadores sinalizados para o risco** - Um [utilizador arriscado](concept-user-at-risk.md) é um indicador para uma conta de utilizador que pode ter sido comprometida.

Este artigo dá-lhe uma visão geral do relatório de inscrição.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="who-can-access-the-data"></a>Quem pode aceder aos dados?

* Utilizadores nas funções de Administrador de Segurança, Leitor de Segurança, Leitor Global e Leitor de Relatórios
* Administradores Globais
* Qualquer utilizador (não administrador) pode aceder aos seus próprios inícios de sessão 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Que licença do Azure AD precisa para aceder à atividade de entrada?

- O relatório de atividades de entrada está disponível em [todas as edições da Azure AD.](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data)

- Se pretender aceder aos dados de entrada utilizando uma API, o seu inquilino deve ter uma licença [Azure Ative Directory Premium](../fundamentals/active-directory-get-started-premium.md) associada a ele.



## <a name="sign-ins-report"></a>Relatório de inscrição

O relatório de inscrição do utilizador fornece respostas às seguintes perguntas:

* O que é o padrão de início de sessão de um utilizador?
* Quantos utilizadores iniciaram sessão ao longo de uma semana?
* Qual é o estado destes inícios de sessão?

No menu do [portal Azure,](https://portal.azure.com) selecione **Azure Ative Directory,** ou procure e selecione **Azure Ative Directory** a partir de qualquer página.

![Selecione Diretório Ativo Azure](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

Sob **monitorização**, selecione **Sign-ins** para abrir o [relatório Desine .](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)

![Atividade de inscrição](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "Atividade de início de sessão")

Pode levar até duas horas para alguns registos de inscrição aparecerem no portal.

> [!IMPORTANT]
> O relatório de entrada de inscrições apenas exibe os **insins interativos,** isto é, os sessão em que um utilizador assina manualmente a utilização do seu nome de utilizador e palavra-passe. Os sign-ins não interativos, tais como a autenticação serviço-a-serviço, não são apresentados no relatório de inscrição. 

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

O diálogo **Das Colunas** dá-lhe acesso aos atributos selecionáveis. Num relatório de inscrição, não pode ter campos que tenham mais de um valor para um determinado pedido de inscrição como coluna. Isto é, por exemplo, verdadeiro para detalhes de autenticação, dados de acesso condicional e localização da rede.   

![Atividade de inscrição](./media/concept-sign-ins/columns.png "Atividade de início de sessão")

Selecione um item na vista da lista para obter informações mais detalhadas.

![Atividade de inscrição](./media/concept-sign-ins/basic-sign-in.png "Atividade de início de sessão")

> [!NOTE]
> Os clientes podem agora resolver as políticas de Acesso Condicional através de todos os relatórios de inscrição. Clicando no separador **Acesso Condicional** para um registo de entrada, os clientes podem rever o estado de Acesso Condicional e mergulhar nos detalhes das políticas que se aplicavam ao registo e ao resultado de cada apólice.
> Para mais informações, consulte as [perguntas frequentes sobre informações sobre a AC em todos os sign-ins](reports-faq.md#conditional-access).



## <a name="filter-sign-in-activities"></a>Filtrar atividades de início de sessão

Primeiro, reduzir os dados reportados a um nível que funcione para si. Em segundo lugar, filtrar os dados de inscrição utilizando o campo de data como filtro predefinido. A Azure AD fornece-lhe uma ampla gama de filtros adicionais que pode definir:

![Atividade de inscrição](./media/concept-sign-ins/04.png "Atividade de início de sessão")

**Solicitar identificação** - A identificação do pedido que lhe interessa.

**Utilizador** - O nome ou o nome principal do utilizador (UPN) do utilizador com quem se preocupa.

**Pedido** - O nome da aplicação alvo.
 
**Estado** - O estado de inscrição que lhe interessa:

- Êxito

- Falha

- Interrompido


**Endereço IP** - O endereço IP do dispositivo utilizado para ligar ao seu inquilino.

**Localização** - A localização em que a ligação foi iniciada a partir de:

- Localidade

- Estado / Província

- País/Região


**Recurso** - O nome do serviço utilizado para o inserição.


**ID** de recursos - A identificação do serviço utilizado para o inserição.


**Aplicativo** de cliente - O tipo de aplicação do cliente usada para ligar ao seu inquilino:

![Filtro de aplicativo cliente](./media/concept-sign-ins/client-app-filter.png)


|Nome|Autenticação moderna|Descrição|
|---|:-:|---|
|SMTP autenticado| |Usado pelos clientes POP e IMAP para enviar mensagens de e-mail.|
|Autodescubra| |Usado pelos clientes outlook e EAS para encontrar e ligar-se a caixas de correio no Exchange Online.|
|Exchange ActiveSync| |Este filtro mostra todas as tentativas de entrada em que o protocolo EAS foi tentado.|
|Browser|![Marcar](./media/concept-sign-ins/check.png)|Mostra todas as tentativas de insessão dos utilizadores que usam navegadores web|
|Exchange ActiveSync| | Mostra todas as tentativas de login dos utilizadores com aplicações de clientes usando o Exchange ActiceSync para se conectar ao Exchange Online|
|Troca de PowerShell online| |Usado para ligar a Exchange Online com powerShell remoto. Se bloquear a autenticação básica para exchange Online PowerShell, precisa de utilizar o módulo Exchange Online PowerShell para se ligar. Para obter instruções, consulte Connect to Exchange Online PowerShell utilizando a [autenticação de vários fatores](https://docs.microsoft.com/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).|
|Serviços Web Exchange| |Uma interface de programação que é usada pelo Outlook, Outlook for Mac e aplicações de terceiros.|
|IMAP4| |Um cliente de correio antigo que usa o IMAP para recuperar e-mail.|
|MAPI sobre HTTP| |Usado pelo Outlook 2010 e mais tarde.|
|Aplicativos móveis e clientes de desktop|![Marcar](./media/concept-sign-ins/check.png)|Mostra todas as tentativas de login dos utilizadores que usam aplicações móveis e clientes de desktop.|
|Livro de endereços offline| |Uma cópia das coleções da lista de endereços que são descarregadas e utilizadas pelo Outlook.|
|Outlook Anywhere (RPC over HTTP)| |Usado pelo Outlook 2016 e mais cedo.|
|Serviço outlook| |Usado pelo aplicativo Mail and Calendar para windows 10.|
|POP3| |Um cliente de correio antigo usando POP3 para recuperar e-mail.|
|Serviços Web de Reporte| |Usado para recuperar dados de relatório sinuoso em Exchange Online.|
|Outros clientes| |Mostra todas as tentativas de login dos utilizadores onde a aplicação do cliente não está incluída ou desconhecida.|



**Sistema operativo** - O sistema operativo em funcionamento no dispositivo utilizou o sinal de inscrição para o seu inquilino. 


**Navegador** do dispositivo - Se a ligação foi iniciada a partir de um navegador, este campo permite filtrar pelo nome do navegador.


**Id correlação** - A identificação da correlação da atividade.




**Acesso condicional** - O estado das regras de acesso condicional aplicadas

- **Não aplicada**: Nenhuma política aplicada ao utilizador e aplicação durante o sessão.

- **Sucesso**: Uma ou mais políticas de acesso condicional aplicadas ao utilizador e aplicação (mas não necessariamente as outras condições) durante o registo. 

- **Falha**: Uma ou mais políticas de acesso condicional aplicadas e não foi satisfeita durante o registo.









## <a name="download-sign-in-activities"></a>Transferir atividades de início de sessão

Clique na opção **Download** para criar um ficheiro CSV ou JSON dos mais recentes 250.000 registos. Comece com [o download dos dados de entrada](quickstart-download-sign-in-report.md) se quiser trabalhar com ele fora do portal Azure.  

![Transferir](./media/concept-sign-ins/71.png "Transferência")

> [!IMPORTANT]
> O número de registos que pode descarregar está limitado pelas políticas de retenção de [relatórios do Azure Ative Directory.](reference-reports-data-retention.md)  


## <a name="sign-ins-data-shortcuts"></a>Atalhos de dados de inscrição

A Azure AD e o portal Azure fornecem-lhe pontos de entrada adicionais para os dados de inscrição:

- A visão geral da proteção da identidade
- Utilizadores
- Grupos
- Aplicações Empresariais

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Utilizadores insaem dados na proteção de segurança da identidade

O gráfico de inscrição do utilizador na página geral de **proteção** de segurança da identidade mostra agregações semanais de inscrições. O padrão para o período de tempo é de 30 dias.

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
- ID da aplicação
- Aplicação
- Cliente
- Localização
- Endereço IP
- Date
- MFA Necessário
- Estado de início de sessão

> [!NOTE]
> Os endereços IP são emitidos de modo a que não exista uma ligação definitiva entre um endereço IP e onde o computador com esse endereço está fisicamente localizado. Mapear endereços IP é complicado pelo facto de fornecedores móveis e VPNs emitirem endereços IP de piscinas centrais que muitas vezes estão muito longe de onde o dispositivo cliente é realmente usado. Atualmente nos relatórios da AD Azure, converter o endereço IP para uma localização física é um esforço melhor baseado em vestígios, dados de registo, retrospetivas inversas e outras informações.

Na página **Utilizadores**, pode obter uma descrição geral completa de todos os inícios de sessão dos utilizadores ao clicar em **Inícios de sessão** na secção **Atividade**.

![Atividade de inscrição](./media/concept-sign-ins/08.png "Atividade de início de sessão")

## <a name="usage-of-managed-applications"></a>Utilização de aplicações geridas

Com uma vista centrada em aplicações dos seus dados de início de sessão, poderá responder a perguntas como:

* Quem está a utilizar as minhas aplicações?
* Quais são as três melhores aplicações da sua organização?
* Como está a minha nova candidatura?

O ponto de entrada para estes dados são as três principais aplicações da sua organização. Os dados estão contidos nos últimos 30 dias no relatório de **visão geral** ao abrigo das **aplicações da Enterprise**.

![Atividade de inscrição](./media/concept-sign-ins/10.png "Atividade de início de sessão")

Os gráficos de utilização de aplicações agregam semanalmente as inscrições para as suas três principais aplicações num determinado período de tempo. A predefinição do período de tempo é 30 dias.

![Atividade de inscrição](./media/concept-sign-ins/graph-chart.png "Atividade de início de sessão")

Se quiser, pode colocar o foco numa aplicação específica.

![Relatórios](./media/concept-sign-ins/single-app-usage-graph.png "Relatórios")

Quando clica num dia no gráfico de utilização da aplicação, obtém uma lista detalhada das atividades de início de sessão.

A opção **Inícios de sessão** dá uma visão geral completa de todos os eventos de início de sessão nas suas aplicações.

## <a name="office-365-activity-logs"></a>Registos de atividades do Office 365

Pode ver registos de atividade do Office 365 a partir do centro de administração da [Microsoft 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Considere que, a atividade do Office 365 e os registos de atividade da Azure AD partilham um número significativo de recursos de diretório. Apenas o centro de administração microsoft 365 fornece uma visão completa dos registos de atividade do Office 365. 

Também pode aceder aos registos de atividade do Office 365 programáticamente utilizando as APIs de [Gestão do Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Passos seguintes

* [Códigos de erro do relatório de atividade sessão](reference-sign-ins-error-codes.md)
* [Políticas de retenção de dados da AD Azure](reference-reports-data-retention.md)
* [AD Azure reporta tardios](reference-reports-latencies.md)

