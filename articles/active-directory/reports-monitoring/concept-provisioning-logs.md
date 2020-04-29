---
title: Registos de fornecimento no portal de Diretório Ativo Azure (pré-visualização) / Microsoft Docs
description: Introdução de relatórios de atividades de provisionamento no portal azure Ative Diretório
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
ms.date: 11/04/2019
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30cc8be6ad9ebffcad58c5b2412ae15ff3f26fa5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81113334"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Relatórios de provisionamento no portal azure ative diretório (pré-visualização)

A arquitetura de reporte em Azure Ative Directory (Azure AD) consiste nos seguintes componentes:

- **Atividade** 
    - **Iniciars inscrições** – Informação sobre a utilização de aplicações geridas e atividades de sessão de utilizadores.
    - **Registos de auditoria Registos** - [de auditoria Registos](concept-audit-logs.md) de auditoria fornecem informações sobre a atividade do sistema sobre utilizadores e gestão de grupos, aplicações geridas e atividades de diretório.
    - **Registos de fornecimento** - Forneça a atividade do sistema sobre utilizadores, grupos e funções que são provisionados pelo serviço de provisionamento da AD Azure. 

- **Segurança** 
    - **Inscrições arriscadas** - Um [sinal de inscrição arriscado](concept-risky-sign-ins.md) é um indicador para uma tentativa de inscrição que poderia ter sido realizada por alguém que não é o legítimo proprietário de uma conta de utilizador.
    - **Utilizadores sinalizados para o risco** - Um [utilizador arriscado](concept-user-at-risk.md) é um indicador para uma conta de utilizador que pode ter sido comprometida.

Este tópico dá-lhe uma visão geral do relatório de prestação.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="who-can-access-the-data"></a>Quem pode aceder aos dados?
* Utilizadores nas funções de Administrador de Segurança, Leitor de Segurança, Leitor de Relatórios, Administrador de Aplicações e Administrador de Aplicações em Nuvem
* Administradores Globais


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>Que licença da Azure AD precisa para aceder a atividades de provisionamento?

O seu inquilino deve ter uma licença Azure AD Premium associada a ela para ver o relatório de atividades de provisionamento. Ver [Começar com o Azure Ative Directory Premium](../fundamentals/active-directory-get-started-premium.md) para atualizar a sua edição de Diretório Ativo Azure. 

## <a name="provisioning-logs"></a>Registos de aprovisionamento

Os registos de fornecimento fornecem respostas às seguintes perguntas:

* Que grupos foram criados com sucesso no ServiceNow?
* Como foram importados os papéis da Amazon Web Services?
* Que utilizadores foram criados sem sucesso no DropBox?

Pode aceder aos registos de provisionamento **selecionando Registos** de Provisionamento na secção **de Monitorização** da lâmina **de diretório Ativo Azure** no [portal Azure](https://portal.azure.com). Pode levar até duas horas para alguns registos de provisionamento aparecerem no portal.

![Registos de aprovisionamento](./media/concept-provisioning-logs/access-provisioning-logs.png "Registos de aprovisionamento")


Um registo de provisionamento tem uma visão de lista padrão que mostra:

- A identidade
- A ação
- O sistema de origem
- O sistema alvo
- O estado
- A data


![Colunas predefinidas](./media/concept-provisioning-logs/default-columns.png "Colunas predefinidas")

Pode personalizar a vista de lista ao clicar em **Colunas** na barra de ferramentas.

![Escolhador de colunas](./media/concept-provisioning-logs/column-chooser.png "Escolhador de colunas")

Isto permite-lhe apresentar campos adicionais ou remover campos que já são apresentados.

![Colunas disponíveis](./media/concept-provisioning-logs/available-columns.png "Colunas disponíveis")

Selecione um item na vista da lista para obter informações mais detalhadas.

![Informações detalhadas](./media/concept-provisioning-logs/steps.png "Filtro")


## <a name="filter-provisioning-activities"></a>Atividades de fornecimento de filtros

Pode filtrar os seus dados de fornecimento. Alguns valores de filtro são povoados dinamicamente com base no seu inquilino. Se, por exemplo, não tiver nenhum evento de criação no seu inquilino, não haverá uma opção de filtro para criar.
Na vista predefinida, pode selecionar os seguintes filtros:

- Identidade
- Date
- Estado
- Ação


![Filtro](./media/concept-provisioning-logs/default-filter.png "Filtro")

O filtro **Identidade** permite especificar o nome ou a identidade com que se preocupa. Esta identidade pode ser um utilizador, grupo, papel ou outro objeto. Pode pesquisar pelo nome ou identificação do objeto. A identificação varia de acordo com o cenário. Por exemplo, ao fornecer um objeto da AD Azure para o SalesForce, o ID de origem é o ID de objeto do utilizador em AD Azure enquanto o TargetID é o ID do utilizador na Salesforce. Ao fornecer do Workday para Ative Directy, o ID fonte é o ID do trabalhador do trabalho. Note que o nome do utilizador pode nem sempre estar presente na coluna Identidade. Sempre haverá uma identificação. 


O filtro **Data** permite-lhe definir um período de tempo para os dados devolvidos.  
Os valores possíveis são:

- 1 mês
- 7 dias
- 30 dias
- 24 horas
- Intervalo de tempo personalizado

Quando selecionar um prazo personalizado, pode configurar uma data de início e uma data de fim.


O filtro **'Estado'** permite selecionar:

- Todos
- Êxito
- Falha
- Ignorado



O filtro **Action** permite filtrar o:

- Criar 
- Atualizar
- Eliminar
- Desativar
- Outros

Além disso, aos filtros da vista predefinida, também pode definir os seguintes filtros:

- ID de trabalho
- ID do ciclo
- Alterar ID
- ID de origem
- ID do alvo
- Aplicação


![Escolha um campo](./media/concept-provisioning-logs/add-filter.png "Escolha um campo")


- **Job ID** - Um ID de trabalho único está associado a cada aplicação que você permitiu o provisionamento.   

- **ID do ciclo** - Identifica exclusivamente o ciclo de provisionamento. Você pode compartilhar este ID para apoiar para olhar para o ciclo em que este evento ocorreu.

- **Alterar ID** - Identificador único para o evento de provisionamento. Você pode partilhar este ID para apoiar para procurar o evento de provisionamento.   


- **Sistema Fonte** - Permite-lhe especificar de onde a identidade está a ser disponibilizada. Por exemplo, ao fornecer um objeto de Azure AD para ServiceNow, o sistema Fonte é Azure AD. 

- **Target System** - Permite-lhe especificar para onde a identidade está a ser disponibilizada. Por exemplo, ao fornecer um objeto de Azure AD para ServiceNow, o Sistema alvo é ServiceNow. 

- **Aplicação** - Permite-lhe mostrar apenas registos de aplicações com um nome de exibição que contenha uma cadeia específica.

 

## <a name="provisioning-details"></a>Fornecer detalhes 

Ao selecionar um item na vista da lista de fornecimento, obtém mais detalhes sobre este item.
Os detalhes são agrupados com base nas seguintes categorias:

- Passos

- Resolução de problemas e recomendações

- Propriedades modificadas

- Resumo


![Filtro](./media/concept-provisioning-logs/provisioning-tabs.png "Separadores")



### <a name="steps"></a>Passos

O separador **Steps** descreve os passos dados para fornecer um objeto. O fornecimento de um objeto pode consistir em quatro etapas: 

- Objeto de importação
- Determine se o objeto está no âmbito
- Combinar objeto entre fonte e alvo
- Objeto de provisão (tomar medidas - isto pode ser uma criação, atualização, eliminação ou desativação)



![Filtro](./media/concept-provisioning-logs/steps.png "Filtro")


### <a name="troubleshoot-and-recommendations"></a>Resolução de problemas e recomendações


O separador de resolução de **problemas e recomendações** fornece o código de erro e a razão. A informação de erro só está disponível em caso de falha. 


### <a name="modified-properties"></a>Propriedades modificadas

As **propriedades modificadas** mostram o valor antigo e o novo valor. Nos casos em que não há valor antigo, a antiga coluna de valor está em branco. 


### <a name="summary"></a>Resumo

O separador **resumo** fornece uma visão geral do que aconteceu e identifica o objeto no sistema de origem e alvo. 

## <a name="what-you-should-know"></a>O que deve saber

- O portal Azure armazena dados de fornecimento por 30 dias se tiver uma edição premium e 7 dias se tiver uma edição gratuita..

- Pode utilizar o atributo de Id de alteração como identificador único. Isto é, por exemplo, útil quando interage com o suporte do produto.

- Atualmente não existe qualquer opção para descarregar dados de fornecimento.

- Atualmente não existe suporte para análise de registos.

- Ao aceder aos registos de fornecimento a partir do contexto de uma aplicação, não filtra automaticamente os eventos para a aplicação específica como os registos de auditoria fazem.

## <a name="error-codes"></a>Códigos de Erro

Utilize a tabela abaixo para entender melhor como resolver erros que pode encontrar nos registos de fornecimento. Para quaisquer códigos de erro que estejam em falta, forneça feedback utilizando o link na parte inferior desta página. 

|Código de Erro|Descrição|
|---|---|
|Conflito, Conflito de Entrada|Corrija os valores de atributos conflituosos em Azure AD ou na aplicação, ou reveja a configuração do atributo correspondente se a conta de utilizador conflituosa fosse compatível e assumido. Reveja a seguinte [documentação](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) para obter mais informações sobre a configuração de atributos correspondentes.|
|TooManyRequests|A aplicação alvo rejeitou esta tentativa de atualizar o utilizador porque está sobrecarregado e recebe muitos pedidos. Não há nada a fazer. Esta tentativa será automaticamente retirada. A Microsoft também foi notificada deste problema.|
|InternalServerError |A aplicação alvo devolveu um erro inesperado. Pode haver um problema de serviço com a aplicação-alvo que está a impedir que isso funcione. Esta tentativa será automaticamente retirada em 40 minutos.|
|Direitos Insuficientes, MétodoNão Permitido, Não Permitido, Não Autorizado| A Azure AD conseguiu autenticar com a aplicação-alvo, mas não foi autorizada a realizar a atualização. Por favor, reveja quaisquer instruções fornecidas pela aplicação-alvo, bem como o respetivo [tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)de aplicação.|
|Entidade Não Processável|A aplicação alvo devolveu uma resposta inesperada. A configuração da aplicação-alvo pode não estar correta, ou pode haver um problema de serviço com a aplicação-alvo que está a impedir que isso funcione.|
|Erro de Protocolo de Exceção web |Ocorreu um erro de protocolo HTTP durante a ligação à aplicação-alvo. Não há nada a fazer. Esta tentativa será automaticamente retirada em 40 minutos.|
|Âncora Inválida|Um utilizador que tenha sido previamente criado ou acompanhado pelo serviço de provisionamento já não existe. Verifique se o utilizador existe. Para forçar uma recorrespondência de todos os utilizadores, utilize a API do Gráfico MS para reiniciar o [trabalho](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Note que o reinício do fornecimento irá desencadear um ciclo inicial, que pode levar algum tempo a concluir. Também elimina a cache que o serviço de provisionamento utiliza para operar, o que significa que todos os utilizadores e grupos do inquilino terão de ser novamente avaliados e que certos eventos de provisionamento poderão ser eliminados.|
|Não Implementado | A aplicação alvo devolveu uma resposta inesperada. A configuração da aplicação pode não estar correta, ou pode haver um problema de serviço com a aplicação alvo que está a impedir que isso funcione. Por favor, reveja quaisquer instruções fornecidas pela aplicação-alvo, bem como o respetivo [tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)de aplicação. |
|Campos Obrigatórios Desaparecidos, Valores Desaparecidos |O utilizador não pôde ser criado porque faltam valores necessários. Corrija os valores de atributo em falta no registo de origem ou reveja a configuração do atributo correspondente para garantir que os campos necessários não são omitidos. [Saiba mais](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) sobre configurar atributos correspondentes.|
|SchemaAttributeNotFound |Não pôde executar a operação porque foi especificado um atributo que não existe na aplicação-alvo. Consulte a [documentação](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) sobre a personalização do atributo e certifique-se de que a sua configuração está correta.|
|InternalError |Ocorreu um erro de serviço interno no serviço de provisionamento da AD Azure. Não há nada a fazer. Esta tentativa será automaticamente novamente experimentada em 40 minutos.|
|Domínio inválido |A operação não pôde ser executada devido a um valor de atributo contendo um nome de domínio inválido. Atualize o nome de domínio no utilizador ou adicione-o à lista permitida na aplicação-alvo. |
|Tempo limite |A operação não pôde ser concluída porque o pedido-alvo demorou demasiado tempo a responder. Não há nada a fazer. Esta tentativa será automaticamente novamente experimentada em 40 minutos.|
|LicenciadoLimite Ultrapassado|O utilizador não pôde ser criado na aplicação-alvo porque não existem licenças disponíveis para este utilizador. Ou obtém licenças adicionais para a aplicação alvo, ou revê as atribuições do utilizador e atribui a configuração de mapeamento para garantir que os utilizadores corretos são atribuídos com os atributos corretos.|
|DuplicarTargetEntries  |A operação não pôde ser concluída porque mais de um utilizador na aplicação-alvo foi encontrado com os atributos de correspondência configurados. Ou remova o utilizador duplicado da aplicação alvo ou reconfigure os mapeamentos do atributo como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).|
|DuplicateSourceEntries | A operação não pôde ser concluída porque mais de um utilizador foi encontrado com os atributos de correspondência configurados. Ou remova o utilizador duplicado ou reconfigure os mapeamentos do atributo como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).|

## <a name="next-steps"></a>Passos seguintes

* [Verifique o estado do fornecimento de utilizadores](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [Problema de configuração do fornecimento de utilizadores a uma aplicação da Galeria AD Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)


