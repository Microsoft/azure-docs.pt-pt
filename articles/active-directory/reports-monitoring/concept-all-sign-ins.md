---
title: Relatórios de atividades de inscrição do Azure Ative Directory - pré-visualização / Microsoft Docs
description: Introdução aos relatórios de atividade de inscrição no portal Azure Ative Directory
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
ms.date: 09/23/2020
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36e6642e230fa809630751e224ff9384ea8524d1
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92319783"
---
# <a name="azure-active-directory-sign-in-activity-reports---preview"></a>Relatórios de atividades de inscrição do Azure Ative Directory - pré-visualização

A arquitetura de reporte no Azure Ative Directory (Azure AD) consiste nos seguintes componentes:

- **Atividade** 
    - **Ins- Ins-** Informações sobre quando os utilizadores, aplicações e recursos geridos insinuam-se na Azure AD e acedem a recursos.
    - **Registos de**  -  auditoria [Os registos de auditoria](concept-audit-logs.md) fornecem informações sobre a atividade do sistema sobre utilizadores e gestão de grupos, aplicações geridas e atividades de diretório.
- **Segurança** 
    - **Entradas arriscadas** - Um [sinal de risco](../identity-protection/overview-identity-protection.md) é um indicador para uma tentativa de inscrição por alguém que não é o legítimo proprietário de uma conta de utilizador.
    - **Utilizadores sinalizados para o risco** - Um [utilizador de risco](../identity-protection/overview-identity-protection.md) é um indicador para uma conta de utilizador que pode ter sido comprometida.

O relatório clássico de inscrições no Azure Ative Directory fornece-lhe uma visão geral das inscrições interativas do utilizador. Além disso, tem agora acesso a três relatórios adicionais de inscrição que estão agora em pré-visualização:

- Insuposições não interativas de utilizadores

- Inscrições principais do serviço

- Identidades geridas para os sign-ins de recursos Azure

Este artigo dá-lhe uma visão geral do relatório de atividade de inscrição com a pré-visualização de identidades não interativas, de aplicação e geridas para os sign-ins de recursos Azure. Para obter informações sobre o relatório de inscrição sem as funcionalidades de pré-visualização, consulte  [os relatórios de atividades de inscrição no portal Azure Ative Directory](concept-sign-ins.md).



## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a utilizar esta funcionalidade, deve saber as respostas para:

- Quem pode aceder aos dados?

- Que licença do Azure AD precisa para aceder à atividade de entrada?

### <a name="who-can-access-the-data"></a>Quem pode aceder aos dados?

- Utilizadores nas funções de Administrador de Segurança, Leitor de Segurança e Leitor de Relatórios

- Administradores Globais

- Qualquer utilizador (não administrador) pode aceder aos seus próprios inícios de sessão 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Que licença do Azure AD precisa para aceder à atividade de entrada?

O seu inquilino deve ter uma licença Azure AD Premium associada a ela para ver atividades de inscrição. Veja [como começar com o Azure Ative Directory Premium](../fundamentals/active-directory-get-started-premium.md) para atualizar a sua edição do Azure Ative Directory. Levará alguns dias para que os dados apareçam nos relatórios depois de atualizar para uma licença premium sem atividades de dados antes da atualização.



## <a name="sign-ins-report"></a>Relatório de inscrições

O relatório de inscrições fornece respostas às seguintes perguntas:

- Qual é o padrão de inscrição de um utilizador, aplicação ou serviço?
- Quantos utilizadores, apps ou serviços assinaram em mais de uma semana?
- Qual é o estado destes inícios de sessão?


Na lâmina de relatório de inscrição, pode alternar entre:

- **Insuposições interativas do utilizador** - Ins-ins em que um utilizador fornece um fator de autenticação, como uma palavra-passe, uma resposta através de uma aplicação MFA, um fator biométrico ou um código QR.

- **Logins não interativos do utilizador** - Logins realizados por um cliente em nome de um utilizador. Estes inserções não requerem qualquer interação ou fator de autenticação por parte do utilizador. Por exemplo, autenticação e autorização usando fichas de atualização e acesso que não requerem que um utilizador introduza credenciais.

- **Iniciars-ins - Inscrições** por apps e diretores de serviço que não envolvam nenhum utilizador. Nestes insusentuques, a app ou serviço fornece uma credencial em seu próprio nome para autenticar ou aceder a recursos.

- **Identidades geridas para a Azure resources sign-ins** - Sign-ins by Azure resources that have secrets managed by Azure. Para mais informações, veja [quais são as identidades geridas para os recursos da Azure?](../managed-identities-azure-resources/overview.md) 


![Tipos de relatórios de inscrição](./media/concept-all-sign-ins/sign-ins-report-types.png)












## <a name="user-sign-ins"></a>Insuposições de utilizador

Cada separador na lâmina de inscrição mostra as colunas predefinidos abaixo. Alguns separadores têm colunas adicionais:

- Data de início de sessão

- ID do Pedido

- Nome de utilizador ou ID do utilizador

- Nome da aplicação ou ID de aplicação

- Estado da inscrição

- Endereço IP do dispositivo utilizado para a inscrição



### <a name="interactive-user-sign-ins"></a>Insuposições interativas do utilizador


As entradas interativas do utilizador são inserções em que um utilizador fornece um fator de autenticação ao Azure AD ou interage diretamente com a AZure AD ou uma aplicação auxiliar, como a aplicação Microsoft Authenticator. Os fatores que os utilizadores fornecem incluem palavras-passe, respostas a desafios de MFA, fatores biométricos ou códigos QR que um utilizador fornece ao Azure AD ou a uma aplicação auxiliar.

Este relatório inclui também as inscrições federadas de fornecedores de identidade que são federadas à Azure AD.  


**Tamanho do relatório:** pequeno <br> 
**Exemplos:**

- Um utilizador fornece o nome de utilizador e a palavra-passe no ecrã de entrada AZure AD.

- Um utilizador passa um desafio SMS MFA.

- Um utilizador fornece um gesto biométrico para desbloquear o seu PC Windows com o Windows Hello for Business.

- Um utilizador é federado para a Azure AD com uma afirmação AD FS SAML.


Além dos campos predefinidos, o relatório de inscrições interativo também mostra: 

- O local de inscrição

- Se o acesso condicional foi aplicado



Pode personalizar a vista de lista ao clicar em **Colunas** na barra de ferramentas.

![Colunas de inscrição interativas do utilizador](./media/concept-all-sign-ins/columns-interactive.png "Colunas de inscrição interativas do utilizador")





A personalização da vista permite-lhe exibir campos adicionais ou remover campos que já estão expostos.

![Todas as colunas interativas](./media/concept-all-sign-ins/all-interactive-columns.png)


Selecione um item na vista da lista para obter informações mais detalhadas sobre o sôm-in relacionado.

![Atividade de inscrição](./media/concept-all-sign-ins/interactive-user-sign-in-details.png "Insuposições interativas do utilizador")



### <a name="non-interactive-user-sign-ins"></a>Insuposições não interativas de utilizadores

As entradas de utilizador não interativas são insusíveis por uma aplicação do cliente ou componentes de SO em nome de um utilizador. Tal como as insuposições interativas do utilizador, estas insiná-los são feitos em nome de um utilizador. Ao contrário dos pedidos de súmis interativos do utilizador, estes inserções não requerem que o utilizador forneça um fator de autenticação. Em vez disso, o dispositivo ou aplicação do cliente usa um símbolo ou código para autenticar ou aceder a um recurso em nome de um utilizador. Em geral, o utilizador irá perceber que estes ins-ins acontecem no contexto da atividade do utilizador.


**Tamanho do relatório:** Grande <br>
**Exemplos:** 

- Uma aplicação de cliente usa um token de atualização OAuth 2.0 para obter um token de acesso.

- Um cliente usa um código de autorização OAuth 2.0 para obter um token de acesso e atualização.

- Um utilizador executa um único sign-on (SSO) para uma web ou aplicação do Windows num AD Azure ligado ao PC.

- Um utilizador assina uma segunda aplicação do Microsoft Office enquanto tem uma sessão num dispositivo móvel usando FOCI (Family of Client IDs).




Além dos campos predefinidos, o relatório de inscrições não interativos também mostra: 

- ID do Recurso

- Número de inscrições agrupadas




Não pode personalizar os campos mostrados neste relatório.


![Colunas desativadas](./media/concept-all-sign-ins/disabled-columns.png "Colunas desativadas")

Para facilitar a digestão dos dados, os eventos de entrada não interativo são agrupados. Os clientes criam frequentemente muitos logins não interativos em nome do mesmo utilizador num curto espaço de tempo, que partilham todas as mesmas características, exceto pelo tempo que a inscrição foi tentada. Por exemplo, um cliente pode obter um token de acesso uma vez por hora em nome de um utilizador. Se o utilizador ou cliente não alterar o estado, o endereço IP, recurso e todas as outras informações são os mesmos para cada pedido de sinal de acesso. Quando o Azure AD regista vários logins idênticos que não a hora e a data, esses logins serão da mesma entidade são agregados numa única linha. Uma linha com múltiplos insus máximos de inscrição (exceto a data e a hora emitidas) terá um valor superior a 1 na coluna #sign-ins. Você pode expandir a linha para ver todos os diferentes sign-ins e seus diferentes selos de tempo. Os insiná-ins são agregados nos utilizadores não interativos quando os seguintes dados correspondem:


- Aplicação

- Utilizador

- Endereço IP

- Estado

- ID do Recurso


Pode:

- Expanda um nó para ver os itens individuais de um grupo.  

- Clique num item individual para ver todos os detalhes 


![Detalhes de inscrição não interativo do utilizador](./media/concept-all-sign-ins/non-interactive-sign-ins-details.png)




## <a name="service-principal-sign-ins"></a>Inscrições principais do serviço

Ao contrário dos pedidos de solução interativas e não interativos, os principais ins de serviço não envolvem um utilizador. Em vez disso, são insusição por qualquer conta não utilizadora, como aplicações ou principais serviços (exceto o acesso de identidade gerido, que estão incluídos apenas no relatório de inscrições de identidade gerido). Nestes insusentuques, a app ou serviço fornece a sua própria credencial, como um certificado ou app secret para autenticar ou aceder a recursos.


**Tamanho do relatório:** Grande <br>
**Exemplos:**

- Um responsável de serviço utiliza um certificado para autenticar e aceder ao Gráfico do Microsoft. 

- Uma aplicação utiliza um segredo de cliente para autenticar no fluxo de credenciais de cliente da OAuth. 


Este relatório tem uma visão de lista padrão que mostra:

- Data de início de sessão

- ID do Pedido

- Nome principal do serviço ou ID

- Estado

- Endereço IP

- Nome do recurso

- ID do Recurso

- Número de inscrições

Não pode personalizar os campos mostrados neste relatório.

![Colunas desativadas](./media/concept-all-sign-ins/disabled-columns.png "Colunas desativadas")

Para facilitar a digestão dos dados nos registos de entrada principal do serviço, os eventos de entrada principal do serviço são agrupados. As inscrições da mesma entidade nas mesmas condições são agregadas numa única linha. Você pode expandir a linha para ver todos os diferentes sign-ins e seus diferentes selos de tempo. Os insiná-ins são agregados no relatório principal do serviço quando os seguintes dados correspondem:

- Nome principal do serviço ou ID

- Estado

- Endereço IP

- Nome de recurso ou ID

Pode:

- Expanda um nó para ver os itens individuais de um grupo.  

- Clique num item individual para ver todos os detalhes 


![Detalhes da coluna](./media/concept-all-sign-ins/service-principals-sign-ins-view.png "Detalhes da coluna")




## <a name="managed-identity-for-azure-resources-sign-ins"></a>Identidade gerida para a Azure recursos sign-ins 

Identidade gerida para os sign-ins de recursos Azure são inscrições que foram realizadas por recursos que têm os seus segredos geridos pela Azure para simplificar a gestão credencial.

**Tamanho do relatório:** Pequeno <br> 
**Exemplos:**

Um VM com credenciais geridas usa Azure AD para obter um Token de acesso.   


Este relatório tem uma visão de lista padrão que mostra:


- Identificação de identidade gerida

- Nome de identidade gerido

- Recurso

- ID do Recurso

- Número de inscrições agrupadas

Não pode personalizar os campos mostrados neste relatório.

Para facilitar a digestão dos dados, as identidades geridas para os recursos Azure assinam em registos, os eventos de login não interativos são agrupados. As inscrições da mesma entidade são agregadas numa única linha. Você pode expandir a linha para ver todos os diferentes sign-ins e seus diferentes selos de tempo. Os ins-ins são agregados no relatório de identidades geridas quando todos os dados seguintes correspondem:

- Nome de identidade gerido ou ID

- Estado

- Endereço IP

- Nome de recurso ou ID

Selecione um item na vista da lista para exibir todos os insus máximos que estão agrupados sob um nó.

Selecione um item agrupado para ver todos os detalhes do sind insundo. 


## <a name="filter-sign-in-activities"></a>Filtrar atividades de início de sessão

Ao configurar um filtro, pode reduzir o âmbito dos dados de inscrição devolvidos. O Azure AD fornece-lhe uma ampla gama de filtros adicionais que pode definir. Ao configurar o filtro, deve sempre prestar especial atenção ao filtro de gama **de datas** configurado. Um filtro de gama de datas adequado garante que o Azure AD apenas devolve os dados que realmente lhe interessa.     

O filtro de gama **Data** permite definir um prazo para os dados devolvidos.
Os valores possíveis são:

- Um mês

- Sete dias

- Vinte e quatro horas

- Personalizado

![Filtro de intervalo de data](./media/concept-all-sign-ins/date-range-filter.png)





### <a name="filter-user-sign-ins"></a>Filtrar as entradas do utilizador

O filtro para insusição interativa e não interativa é o mesmo. Por isso, o filtro configurado para os insus e ins interativos é persistido para os insus e vice-versa não interativos. 






## <a name="access-the-new-sign-in-activity-reports"></a>Aceda aos novos relatórios de atividades de inscrição 

O relatório de atividade de inscrição no portal Azure fornece-lhe um método simples para ligar e desligar o relatório de pré-visualização. Se tiver os relatórios de pré-visualização ativados, obtém um novo menu que lhe dá acesso a todos os tipos de relatórios de atividades de inscrição.     


Para aceder aos novos relatórios de inscrição com inscrições não interativas e de aplicação: 

1. No [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory**.

    ![Selecione Azure AD](./media/concept-all-sign-ins/azure-services.png)

2. Na secção **'Monitorização',** clique **em Iniciars-ins**.

    ![Selecione ins](./media/concept-all-sign-ins/sign-ins.png)

3. Clique na barra **de pré-visualização.**

    ![Permitir uma nova vista](./media/concept-all-sign-ins/enable-new-preview.png)

4. Para voltar a mudar para a vista predefinida, clique novamente na barra **de pré-visualização.** 

    ![Restaurar a vista clássica](./media/concept-all-sign-ins/switch-back.png)







## <a name="download-sign-in-activity-reports"></a>Baixar relatórios de atividades de inscrição

Quando descarrega um relatório de atividade de inscrição, o seguinte é verdade:

- Pode descarregar o relatório de inscrição como ficheiro CSV ou JSON.

- Você pode baixar até 100-K registos. Se quiser descarregar mais dados, utilize a API de reporte.

- O seu download baseia-se na seleção de filtros que fez.

- O número de registos que pode descarregar está limitado pelas políticas de retenção do [relatório Azure Ative Directory](reference-reports-data-retention.md). 


![Relatórios de descarregamento](./media/concept-all-sign-ins/download-reports.png "Relatórios de descarregamento")


Cada download de CSV é composto por seis ficheiros diferentes:

- Insuposições interativas

- Detalhes auth dos sign-ins interativos

- Insuposições não interativas

- Detalhes auth dos sign-ins não interativos

- Inscrições principais do serviço

- Identidade gerida para a Azure recursos sign-ins

Cada download JSON consiste em quatro ficheiros diferentes:

- Insuposições interativas (inclui detalhes auth)

- Insuposições não interativas (inclui detalhes auth)

- Inscrições principais do serviço

- Identidade gerida para a Azure recursos sign-ins

![Transferir ficheiros](./media/concept-all-sign-ins/download-files.png "Transferir ficheiros")




## <a name="next-steps"></a>Passos seguintes

* [Códigos de erro do relatório de atividade de inscrição](reference-sign-ins-error-codes.md)
* [Políticas de retenção de dados Azure AD](reference-reports-data-retention.md)
* [Azure AD reporta latências](reference-reports-latencies.md)