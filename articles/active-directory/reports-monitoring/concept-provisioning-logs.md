---
title: Provisionamento de registos no portal Azure Ative Directory (pré-visualização) Microsoft Docs
description: Introdução aos relatórios de atividade de provisionamento no portal Azure Ative Directory
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81113334"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Relatórios de provisionamento no portal Azure Ative Directory (pré-visualização)

A arquitetura de reporte no Azure Ative Directory (Azure AD) consiste nos seguintes componentes:

- **Atividade** 
    - **Insuposições** – Informações sobre o uso de aplicações geridas e atividades de inscrição do utilizador.
    - **Registos de**  -  auditoria [Os registos de auditoria](concept-audit-logs.md) fornecem informações sobre a atividade do sistema sobre os utilizadores e a gestão do grupo, aplicações geridas e atividades de diretório.
    - **Registos de provisionamento** - Fornecer atividade do sistema sobre utilizadores, grupos e funções que são a provisionadas pelo serviço de fornecimento de Azure AD. 

- **Segurança** 
    - **Entradas arriscadas** - Um [sinal de risco](concept-risky-sign-ins.md) é um indicador para uma tentativa de inscrição que pode ter sido realizada por alguém que não é o legítimo proprietário de uma conta de utilizador.
    - **Utilizadores sinalizados para o risco** - Um [utilizador de risco](concept-user-at-risk.md) é um indicador para uma conta de utilizador que pode ter sido comprometida.

Este tópico dá-lhe uma visão geral do relatório de provisionamento.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="who-can-access-the-data"></a>Quem pode aceder aos dados?
* Utilizadores nas funções de Administrador de Segurança, Leitor de Segurança, Leitor de Relatórios, Administrador de Aplicações e Administrador de Aplicações em Nuvem
* Administradores Globais


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>Qual é a licença AZURE AD que precisa para aceder a atividades de provisionamento?

O seu inquilino deve ter uma licença Azure AD Premium associada a ela para ver o relatório de atividades de provisionamento. Veja [como começar com o Azure Ative Directory Premium](../fundamentals/active-directory-get-started-premium.md) para atualizar a sua edição do Azure Ative Directory. 

## <a name="provisioning-logs"></a>Registos de aprovisionamento

Os registos de provisionamento fornecem respostas às seguintes questões:

* Que grupos foram criados com sucesso no ServiceNow?
* Como os papéis foram importados da Amazon Web Services?
* Que utilizadores foram criados sem sucesso no DropBox?

Pode aceder aos registos de provisionamento selecionando **Registos de Provisionamento** na secção de **Monitorização** da lâmina do **Diretório Ativo Azure** no [portal Azure](https://portal.azure.com). Pode levar até duas horas para que alguns registos de provisionamento apareçam no portal.

![Registos de aprovisionamento](./media/concept-provisioning-logs/access-provisioning-logs.png "Registos de aprovisionamento")


Um registo de provisionamento tem uma visão de lista padrão que mostra:

- A identidade
- A ação
- O sistema de origem
- O sistema alvo
- O estado
- A data


![Colunas predefinidos](./media/concept-provisioning-logs/default-columns.png "Colunas predefinidos")

Pode personalizar a vista de lista ao clicar em **Colunas** na barra de ferramentas.

![Escolha de coluna](./media/concept-provisioning-logs/column-chooser.png "Escolha de coluna")

Isto permite-lhe apresentar campos adicionais ou remover campos que já são apresentados.

![Colunas disponíveis](./media/concept-provisioning-logs/available-columns.png "Colunas disponíveis")

Selecione um item na vista da lista para obter informações mais detalhadas.

![Informações detalhadas](./media/concept-provisioning-logs/steps.png "Filtrar")


## <a name="filter-provisioning-activities"></a>Atividades de provisão de filtros

Pode filtrar os seus dados de atenção. Alguns valores de filtro são dinamicamente povoados com base no seu inquilino. Se, por exemplo, não tiver nenhum evento de criação no seu inquilino, não haverá uma opção de filtro para criar.
Na vista predefinitiva, pode selecionar os seguintes filtros:

- Identidade
- Data
- Estado
- Ação


![Filtro](./media/concept-provisioning-logs/default-filter.png "Filtrar")

O filtro **identidade** permite especificar o nome ou a identidade que lhe interessa. Esta identidade pode ser um utilizador, grupo, papel ou outro objeto. Pode pesquisar pelo nome ou identificação do objeto. O ID varia por cenário. Por exemplo, ao aprovisionar um objeto do Azure AD à SalesForce, o ID de Origem é o ID do objeto do utilizador em Azure AD enquanto o TargetID é o ID do utilizador em Salesforce. Ao ser fornecida de Workday a Ative Directory, o ID de Origem é o ID do trabalhador do Workday. Note que o nome do utilizador pode nem sempre estar presente na coluna Identidade. Haverá sempre uma identificação. 


O filtro **Data** permite-lhe definir um período de tempo para os dados devolvidos.  
Os valores possíveis são:

- 1 mês
- 7 dias
- 30 dias
- 24 horas
- Intervalo de tempo personalizado

Quando selecionar um prazo personalizado, pode configurar uma data de início e uma data de fim.


O filtro **'Estado'** permite-lhe selecionar:

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

Além disso, para os filtros da vista padrão, também pode definir os seguintes filtros:

- ID da tarefa
- ID do ciclo
- Alterar ID
- ID de origem
- ID do alvo
- Aplicação


![Escolha um campo](./media/concept-provisioning-logs/add-filter.png "Escolha um campo")


- **ID de trabalho** - Um ID de trabalho único está associado a cada aplicação que você habilitado a provisão.   

- **ID de ciclo** - Identifica exclusivamente o ciclo de provisionamento. Você pode compartilhar este ID para apoiar para olhar para o ciclo em que este evento ocorreu.

- **Alterar ID** - Identificador exclusivo para o evento de provisionamento. Você pode compartilhar este ID para apoiar para procurar o evento de provisionamento.   


- **Sistema fonte** - Permite especificar de onde a identidade está a ser fornecida. Por exemplo, ao atrasar um objeto do AZure AD ao ServiceNow, o sistema Source é Azure AD. 

- **Sistema alvo** - Permite especificar para onde a identidade está a ser a provisionada. Por exemplo, ao atrasar um objeto do AZure AD ao ServiceNow, o Sistema Alvo é o ServiceNow. 

- **Aplicação** - Permite-lhe mostrar apenas registos de aplicações com um nome de exibição que contém uma cadeia específica.

 

## <a name="provisioning-details"></a>Provisionamento de detalhes 

Ao selecionar um item na vista da lista de provisionamento, obtém mais detalhes sobre este item.
Os detalhes são agrupados com base nas seguintes categorias:

- Passos

- Resolução de problemas e recomendações

- Propriedades modificadas

- Resumo


![Filtro](./media/concept-provisioning-logs/provisioning-tabs.png "Separadores")



### <a name="steps"></a>Passos

O separador **Passos** descreve os passos dados para a disposição de um objeto. O provisionamento de um objeto pode consistir em quatro etapas: 

- Objeto de importação
- Determinar se o objeto está no âmbito
- Objeto de correspondência entre a fonte e o alvo
- Objeto de provisão (tome medidas - isto pode ser uma criação, atualização, eliminação ou desativação)



![Filtro](./media/concept-provisioning-logs/steps.png "Filtrar")


### <a name="troubleshoot-and-recommendations"></a>Resolução de problemas e recomendações


O **separador de resolução de problemas e recomendações** fornece o código de erro e a razão. A informação de erro só está disponível em caso de falha. 


### <a name="modified-properties"></a>Propriedades modificadas

As **propriedades modificadas** mostram o valor antigo e o novo valor. Nos casos em que não há valor antigo, a coluna de valor antigo está em branco. 


### <a name="summary"></a>Resumo

O **separador resumo** fornece uma visão geral do que aconteceu e identifica o objeto no sistema de origem e alvo. 

## <a name="what-you-should-know"></a>O que deve saber

- As lojas do portal Azure reportaram dados de provisionamento durante 30 dias se tiver uma edição premium e 7 dias se tiver uma edição gratuita..

- Pode utilizar o atributo Change ID como identificador único. Isto é, por exemplo, útil quando interage com o suporte do produto.

- Atualmente não existe opção para descarregar dados de fornecimento.

- Atualmente, não existe suporte para análise de registos.

- Quando acede aos registos de provisionamento a partir do contexto de uma aplicação, não filtra automaticamente eventos para a aplicação específica da forma como os registos de auditoria fazem.

## <a name="error-codes"></a>Códigos de Erro

Utilize a tabela abaixo para entender melhor como resolver os erros que pode encontrar nos registos de provisionamento. Para quaisquer códigos de erro que faltem, forneça feedback utilizando o link na parte inferior desta página. 

|Código de Erro|Descrição|
|---|---|
|Conflito, Conflito de Entrada|Corrija os valores de atributos contraditórios em AD ou na aplicação, ou reveja a configuração do seu atributo correspondente se a conta de utilizador conflituosa for correspondida e assumida. Reveja a seguinte [documentação](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) para obter mais informações sobre a configuração de atributos correspondentes.|
|TooManyRequests|A aplicação-alvo rejeitou esta tentativa de atualizar o utilizador por estar sobrecarregado e receber demasiados pedidos. Não há nada a fazer. Esta tentativa será automaticamente retirada. A Microsoft também foi notificada deste problema.|
|InternalServerError |A aplicação-alvo devolveu um erro inesperado. Pode haver um problema de serviço com a aplicação-alvo que está a impedir que isso funcione. Esta tentativa será automaticamente retirada em 40 minutos.|
|Direitos insuficientes, MétodoNotAllowed, Não Autorizado, Não Autorizado| A Azure AD conseguiu autenticar-se com a aplicação-alvo, mas não estava autorizada a realizar a atualização. Por favor, reveja todas as instruções fornecidas pelo pedido-alvo, bem como o respetivo [tutorial de](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)aplicação.|
|Entidade Não Processável|O pedido de destino devolveu uma resposta inesperada. A configuração da aplicação-alvo pode não estar correta, ou pode haver um problema de serviço com a aplicação-alvo que está a impedir que isso funcione.|
|WebExceptionProtocolError |Ocorreu um erro de protocolo HTTP durante a ligação à aplicação-alvo. Não há nada a fazer. Esta tentativa será automaticamente retirada em 40 minutos.|
|InvalidAnchor|Um utilizador que tenha sido previamente criado ou correspondido pelo serviço de fornecimento já não existe. Verifique se o utilizador existe. Para forçar uma nova correspondência de todos os utilizadores, utilize a API ms graph para reiniciar o [trabalho](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Note que o reatamento irá desencadear um ciclo inicial, que pode levar tempo a ser concluído. Elimina igualmente a cache que o serviço de fornecimento utiliza para funcionar, o que significa que todos os utilizadores e grupos do arrendatário terão de ser novamente avaliados e certos eventos de provisionamento poderão ser eliminados.|
|Não ÉDopliizado | A aplicação-alvo devolveu uma resposta inesperada. A configuração da aplicação pode não estar correta, ou pode haver um problema de serviço com a aplicação-alvo que está a impedir que isso funcione. Por favor, reveja todas as instruções fornecidas pelo pedido-alvo, bem como o respetivo [tutorial de](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)aplicação. |
|Aeródromos ObrigatóriasMissing, MissingValues |O utilizador não pôde ser criado porque faltam valores necessários. Corrija os valores de atributos em falta no registo de origem ou reveja a configuração do seu atributo correspondente para garantir que os campos necessários não sejam omitidos. [Saiba mais](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) sobre configurar atributos correspondentes.|
|SchemaAttributeNotFound |Não foi possível efetuar a operação porque foi especificado um atributo que não existe na aplicação-alvo. Consulte a [documentação](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) sobre a personalização do atributo e certifique-se de que a sua configuração está correta.|
|InternalError |Ocorreu um erro de serviço interno no serviço de fornecimento de Azure AD. Não há nada a fazer. Esta tentativa será automaticamente novamente julgada em 40 minutos.|
|InvalidDomain |A operação não pôde ser realizada devido a um valor de atributo que continha um nome de domínio inválido. Atualize o nome de domínio no utilizador ou adicione-o à lista permitida na aplicação-alvo. |
|Tempo Limite |A operação não pôde ser concluída porque o pedido-alvo demorou demasiado tempo a responder. Não há nada a fazer. Esta tentativa será automaticamente novamente julgada em 40 minutos.|
|LicençaLimitExceed|O utilizador não pôde ser criado na aplicação-alvo porque não existem licenças disponíveis para este utilizador. Ou obtém licenças adicionais para a aplicação-alvo, ou revê as suas atribuições de utilizador e atribui a configuração de mapeamento para garantir que os utilizadores corretos são atribuídos com os atributos corretos.|
|DuplicateTargets  |A operação não pôde ser concluída porque mais de um utilizador na aplicação-alvo foi encontrado com os atributos de correspondência configurados. Remova o utilizador duplicado da aplicação-alvo ou reconfigure os mapeamentos do seu atributo como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).|
|DuplicateSourceEntries | A operação não pôde ser concluída porque mais de um utilizador foi encontrado com os atributos de correspondência configurados. Remova o utilizador duplicado ou reconfigure os mapeamentos do seu atributo como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).|

## <a name="next-steps"></a>Próximos passos

* [Verifique o estado do fornecimento do utilizador](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [Problema de configuração do fornecimento de um utilizador a uma aplicação da Galeria AD Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)


