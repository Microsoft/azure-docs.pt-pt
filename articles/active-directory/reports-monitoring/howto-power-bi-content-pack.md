---
title: Como utilizar o Pacote de Conteúdos do Power BI para o Azure Active Directory | Microsoft Docs
description: Saiba como utilizar o Pacote de Conteúdos do Power BI Para o Azure Active Directory
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 934562147fedcc81b16fd1ad2534af5662ef4b78
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58436834"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Como utilizar o pacote de conteúdos do Azure Active Directory Power BI

|  |
|--|
|Atualmente, o pacote de conteúdos do Power BI para o Azure AD utiliza as APIs do Azure AD Graph para obter dados do seu inquilino do Azure AD. Como resultado, poderá observar alguma disparidade entre os dados disponíveis no pacote de conteúdos e os dados obtidos com as [APIs do Microsoft Graph para relatórios](concept-reporting-api.md). |
|  |

O Power BI conteúdo de pacote para o Azure Active Directory (Azure AD) contém relatórios criados previamente para o ajudar a compreender como os utilizadores adotam e utilizam as funcionalidades do Azure AD. Isto permite-lhe obter informações sobre todas as atividades em seu diretório, com a experiência Rica visualização no Power BI. Também pode criar seu próprio dashboard e partilhá-lo com qualquer pessoa na sua organização. 

## <a name="prerequisites"></a>Pré-requisitos

Precisa de uma licença de premium (P1/P2) do Azure AD para utilizar o pacote de conteúdos. Ver [introdução ao Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) para atualizar a sua edição do Azure Active Directory.

## <a name="install-the-content-pack"></a>Instalar o pacote de conteúdos

Veja a [guia de introdução](quickstart-install-power-bi-content-pack.md) para instalar o pacote de conteúdos do Azure AD Power BI.

### <a name="reports-included-in-this-version-of-azure-ad-logs-content-pack"></a>Pacote de conteúdos de registos de relatórios incluídos nesta versão do Azure AD

Os seguintes relatórios estão incluídos no pacote de conteúdos do Azure AD Power BI. Os relatórios contêm dados a partir da **últimos 30 dias**.

**Relatório de utilização da aplicação e as tendências**:  Este relatório fornece informações sobre as aplicações utilizadas na sua organização. Pode obter uma lista dos aplicativos mais populares ou compreender como uma aplicação que tenha implementado recentemente na sua organização está a ser utilizada. Isto permite-lhe controlar e melhorar a utilização ao longo do tempo.

**Inícios de sessão por localização e utilizadores**: Este relatório fornece dados sobre todos os inícios de sessão executados usando a identidade do Azure. Com este relatório, pode desagregar para inícios de sessão individuais e responder a perguntas como:

- Onde este utilizador inícios de sessão de?
- Que utilizador iniciou sessão mais vezes e de onde é que o fez? 
- O início de sessão foi bem-sucedido?  
 
Também pode filtrar os resultados ao selecionar uma data ou localização específica.

**Utilizadores exclusivos por aplicação**:  Este relatório fornece uma vista de todos os utilizadores exclusivos com uma determinada aplicação. Incluir apenas os utilizadores que têm "*com êxito*" conectado a uma aplicação.

**Inícios de sessão de dispositivo**: Este relatório ajuda-o a compreender os vários perfis de dispositivo utilizados na sua organização e determinar políticas de dispositivos com base na utilização. Ele fornece dados sobre o tipo de SO e os browsers utilizados para início de sessão para aplicações, juntamente com informações detalhadas sobre os utilizadores, incluindo:

- Nome de Utilizador
- Endereço IP
- Localização 
- Estado de início de sessão 

**Funil SSPR**: Este relatório ajuda a compreender como a ferramenta SSPR é utilizada na sua organização. Pode ver a palavra-passe quantas reposições foram tentadas com a ferramenta SSPR e quantas foram bem-sucedidas. Também pode aprofundar mais as falhas de reposições de palavra-passe e compreender por que motivo ocorreram determinadas falhas. 

## <a name="customize-azure-ad-activity-content-pack"></a>Personalizar o pacote de conteúdos de atividades do Azure AD

**Alterar a visualização**:  Pode alterar uma visualização de relatório clicando **Editar relatório** e selecione a visualização que pretende.
 
![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/howto-power-bi-content-pack/09.png) 
 
![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/howto-power-bi-content-pack/10.png) 

**Incluir campos adicionais**:  Pode adicionar um campo para o relatório ou removê-lo ao selecionar o elemento visual para o qual pretende adicionar ou remover o campo. Por exemplo, pode adicionar o campo "sign-in status" para a exibição de tabela, conforme mostrado abaixo. 
 
![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/howto-power-bi-content-pack/11.png) 

**Afixar visualizações ao dashboard**:  Pode personalizar o seu dashboard, incluindo as suas próprias visualizações ao relatório e afixá-lo ao dashboard. 

![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/howto-power-bi-content-pack/13.png) 
 
**Partilhar o dashboard**: Também pode partilhar o dashboard com os utilizadores na sua organização. Depois de partilhar o relatório, os utilizadores podem ver os campos que selecionou no relatório.
 
![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/howto-power-bi-content-pack/14.png) 

## <a name="schedule-a-daily-refresh-of-your-power-bi-report"></a>Agendar uma atualização diária do seu relatório do Power BI

Para agendar uma atualização diária do seu relatório do Power BI, aceda a **conjuntos de dados** > **definições** > **agendar atualização** e defini-lo, conforme mostrado abaixo.
 
![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/howto-power-bi-content-pack/15.png) 

## <a name="update-to-newer-version-of-content-pack"></a>Atualizar para a versão mais recente do pacote de conteúdos

Se pretender atualizar o pacote de conteúdos para uma versão mais recente:

- Baixe o novo pacote de conteúdos e configurá-lo com as instruções neste artigo.

- Assim que tiver configurado para ele, aceda a **origem de dados** > **definições** > **credenciais da origem de dados** e volte a introduzir as suas credenciais.

    ![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/howto-power-bi-content-pack/16.png) 

Depois de verificar se a nova versão do pacote de conteúdos funciona conforme esperado, pode remover a antiga versão, se necessário, eliminando os relatórios e conjuntos de dados associados esse pacote de conteúdos subjacentes.

## <a name="troubleshoot-content-pack-errors"></a>Resolver erros de pacote de conteúdos

Ao trabalhar com o pacote de conteúdos, é possível que se deparar com os seguintes erros: 

- [Falha na atualização](#refresh-failed) 
- [Falha ao atualizar as credenciais de origem de dados](#failed-to-update-data-source-credentials) 
- [Importação de dados está a demorar demasiado tempo](#data-import-is-too-slow) 

Para obter ajuda geral com o Power BI, veja estes [artigos de ajuda](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

### <a name="refresh-failed"></a>A atualização falhou 
 
**Como este erro aparece**: E-mail do Power BI ou no histórico de atualização do status de falha. 


| Causa | Como corrigir |
| ---   | ---        |
| Falha de erros podem ser provocados quando as credenciais dos utilizadores a ligar ao pacote de conteúdos tem sido repor mas não atualizadas nas definições de ligação do pacote de conteúdos de atualização. | No Power BI, localize o conjunto de dados correspondente para o dashboard de registos de atividade do Azure AD (**registos de atividades de diretório Active Directory do Azure**), selecione a atualização de agenda e, em seguida, introduza as credenciais do Azure AD. |
| Uma atualização pode falhar devido a problemas de dados no pacote de conteúdos subjacente. | [Enviar um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
 
### <a name="failed-to-update-data-source-credentials"></a>Falha ao atualizar as credenciais de origem de dados 
 
**Como este erro aparece**: No Power BI, quando se liga para o pacote de conteúdos de registos de atividade do Azure AD. 

| Causa | Como corrigir |
| ---   | ---        |
| O utilizador de ligação não é um administrador global ou um leitor de segurança ou um administrador de segurança. | Utilize uma conta que seja um administrador global ou um leitor de segurança ou um administrador de segurança para aceder os pacotes de conteúdos. |
| O inquilino não é um inquilino de Premium ou não tem pelo menos um utilizador com o ficheiro de licença de Premium. | [Enviar um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
### <a name="data-import-is-too-slow"></a>Importação de dados é muito lenta 
 
**Como este erro aparece**: No Power BI, depois de ligar o seu pacote de conteúdos, o processo de importação de dados começa a preparar o seu dashboard para a atividade do Azure AD registos. Verá a mensagem: **Importar dados...**  sem qualquer progresso adicional.  

| Causa | Como corrigir |
| ---   | ---        |
| Dependendo do tamanho do seu inquilino, este passo pode demorar entre alguns minutos e 30 minutos. | Se a mensagem não é alterado para mostrar o seu dashboard dentro de uma hora [enviar um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md).|
  
## <a name="next-steps"></a>Passos Seguintes

* [Instalar o pacote de conteúdos do Power BI](quickstart-install-power-bi-content-pack.md).
* [Quais são os relatórios do Azure AD? ](overview-reports.md).
