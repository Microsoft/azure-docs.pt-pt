---
title: Como utilizar o Pacote de Conteúdos do Power BI para o Azure Active Directory | Microsoft Docs
description: Saiba como utilizar o Pacote de Conteúdos do Power BI Para o Azure Active Directory
services: active-directory
author: cawrites
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d56802e96028b6b01b6be749405c56df2648161
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988238"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Como usar o pacote de conteúdo do Azure Active Directory Power BI

|  |
|--|
|Atualmente, o pacote de conteúdos do Power BI para o Azure AD utiliza as APIs do Azure AD Graph para obter dados do seu inquilino do Azure AD. Como resultado, poderá observar alguma disparidade entre os dados disponíveis no pacote de conteúdos e os dados obtidos com as [APIs do Microsoft Graph para relatórios](concept-reporting-api.md). |
|  |

O pacote de conteúdo do Power BI para o Azure Active Directory (AD do Azure) contém relatórios pré-criados para ajudá-lo a entender como os usuários adotam e usam os recursos do Azure AD. Isso permite que você se aprofunde em todas as atividades em seu diretório, usando a experiência de visualização avançada no Power BI. Você também pode criar seu próprio painel e compartilhá-lo com qualquer pessoa em sua organização. 

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de uma licença do Azure AD Premium (P1/P2) para usar o pacote de conteúdo. Consulte [introdução ao Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) para atualizar sua edição do Azure Active Directory.

## <a name="install-the-content-pack"></a>Instalar o pacote de conteúdo

Confira o guia de [início rápido](quickstart-install-power-bi-content-pack.md) para instalar o pacote de conteúdo do Azure ad Power bi.

### <a name="reports-included-in-this-version-of-azure-ad-logs-content-pack"></a>Relatórios incluídos nesta versão do pacote de conteúdo de logs do Azure AD

Os relatórios a seguir estão incluídos no pacote de conteúdo do Azure AD Power BI. Os relatórios contêm dados dos **últimos 30 dias**.

**Relatório de uso e tendências do aplicativo**:  Este relatório fornece informações sobre os aplicativos usados em sua organização. Você pode obter uma lista dos aplicativos mais populares ou entender como um aplicativo que você distribuiu recentemente em sua organização está sendo usado. Isso permite que você acompanhe e melhore o uso ao longo do tempo.

**Entradas por local e usuários**: Este relatório fornece dados sobre todas as entradas executadas usando a identidade do Azure. Com esse relatório, você pode fazer uma busca detalhada de entradas individuais e responder a perguntas como:

- De onde foi feito o logon deste usuário?
- Que utilizador iniciou sessão mais vezes e de onde é que o fez? 
- O início de sessão foi bem-sucedido?  
 
Você também pode filtrar os resultados selecionando uma data ou um local específico.

**Usuários exclusivos por aplicativo**:  Este relatório fornece uma exibição de todos os usuários exclusivos usando um determinado aplicativo. Ele inclui apenas os usuários que entraram "*com êxito*" em um aplicativo.

**Entradas do dispositivo**: Este relatório ajuda você a entender os vários perfis de dispositivo usados em sua organização e determinar as políticas de dispositivo com base no uso. Ele fornece dados sobre o tipo de sistema operacional e OS navegadores usados para entrar em aplicativos, juntamente com informações detalhadas sobre os usuários, incluindo:

- Nome de utilizador
- Endereço IP
- Location 
- Estado de início de sessão 

**Funil de SSPR**: Este relatório ajuda você a entender como a ferramenta SSPR é usada na sua organização. Você pode exibir quantas redefinições de senha foram tentadas por meio da ferramenta SSPR e quantas delas foram bem-sucedidas. Você também pode se aprofundar nas falhas de redefinição de senha e entender por que determinadas falhas ocorreram. 

## <a name="customize-azure-ad-activity-content-pack"></a>Personalizar o pacote de conteúdo de atividade do Azure AD

**Alterar a visualização**:  Você pode alterar uma visualização de relatório clicando em **Editar relatório** e selecionando a visualização desejada.
 
![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/howto-power-bi-content-pack/09.png) 
 
![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/howto-power-bi-content-pack/10.png) 

**Incluir campos adicionais**:  Você pode adicionar um campo ao relatório ou removê-lo selecionando o Visual ao qual você deseja adicionar/remover o campo. Por exemplo, você pode adicionar o campo "status de entrada" à exibição de tabela, conforme mostrado abaixo. 
 
![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/howto-power-bi-content-pack/11.png) 

**Fixe visualizações no painel**:  Você pode personalizar seu painel incluindo suas próprias visualizações para o relatório e fixá-las no painel. 

![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/howto-power-bi-content-pack/13.png) 
 
**Compartilhando o painel**: Você também pode compartilhar o painel com os usuários em sua organização. Depois de compartilhar o relatório, os usuários poderão ver os campos que você selecionou no relatório.
 
![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/howto-power-bi-content-pack/14.png) 

## <a name="schedule-a-daily-refresh-of-your-power-bi-report"></a>Agendar uma atualização diária de seu relatório de Power BI

Para agendar uma atualização diária de seu relatório de Power bi, vá para definições de **conjuntos** > de de**configurações** > **agendar atualização** e defina-o como mostrado abaixo.
 
![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/howto-power-bi-content-pack/15.png) 

## <a name="update-to-newer-version-of-content-pack"></a>Atualizar para versão mais recente do pacote de conteúdo

Se você quiser atualizar seu pacote de conteúdo para uma versão mais recente:

- Baixe o novo pacote de conteúdo e configure-o usando as instruções neste artigo.

- Depois de configurá-lo, vá para**configurações** > da **fonte** > de dados**credenciais da fonte de dados** e insira novamente suas credenciais.

    ![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/howto-power-bi-content-pack/16.png) 

Depois de verificar se a nova versão do pacote de conteúdo funciona conforme o esperado, você pode remover a versão antiga, se necessário, excluindo os relatórios subjacentes e os conjuntos de linhas associados a esse pacote de conteúdo.

## <a name="troubleshoot-content-pack-errors"></a>Solucionar erros de pacote de conteúdo

Ao trabalhar com o pacote de conteúdo, é possível que você execute os seguintes erros: 

- [Falha na atualização](#refresh-failed) 
- [Falha ao atualizar as credenciais da fonte de dados](#failed-to-update-data-source-credentials) 
- [A importação de dados está demorando muito](#data-import-is-too-slow) 

Para obter ajuda geral com o Power BI, veja estes [artigos de ajuda](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

### <a name="refresh-failed"></a>A atualização falhou 
 
**Como esse erro é**exibido: Email de Power BI ou status com falha no histórico de atualização. 


| Causa | Como corrigir |
| ---   | ---        |
| Erros de falha na atualização podem ser causados quando as credenciais dos usuários que se conectam ao pacote de conteúdo tiverem sido redefinidas, mas não atualizadas nas configurações de conexão do pacote de conteúdo. | Em Power BI, localize o conjunto de registros correspondente ao painel de logs de atividades do Azure AD (**logs de atividades do Azure Active Directory**), escolha agendar atualização e insira suas credenciais do Azure AD. |
| Uma atualização pode falhar devido a problemas de dados no pacote de conteúdo subjacente. | [Arquivo um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
 
### <a name="failed-to-update-data-source-credentials"></a>Falha ao atualizar as credenciais da fonte de dados 
 
**Como esse erro é**exibido: No Power BI, quando você se conecta ao pacote de conteúdo dos logs de atividades do Azure AD. 

| Causa | Como corrigir |
| ---   | ---        |
| O usuário que está se conectando não é um administrador global ou um leitor de segurança ou um administrador de segurança. | Use uma conta que seja um administrador global ou um leitor de segurança ou um administrador de segurança para acessar os pacotes de conteúdo. |
| Seu locatário não é um locatário Premium ou não tem pelo menos um usuário com o arquivo de licença Premium. | [Arquivo um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
### <a name="data-import-is-too-slow"></a>A importação de dados está muito lenta 
 
**Como esse erro é**exibido: No Power BI, depois de conectar o pacote de conteúdo, o processo de importação de dados começa a preparar seu painel para os logs de atividades do Azure AD. Verá a mensagem: **Importando dados...** sem nenhum progresso adicional.  

| Causa | Como corrigir |
| ---   | ---        |
| Dependendo do tamanho do seu locatário, essa etapa pode levar de alguns minutos a 30 minutos. | Se a mensagem não for alterada para mostrar o painel em uma hora, faça [um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md).|
  
## <a name="next-steps"></a>Passos seguintes

* [Instale o pacote de conteúdo do Power bi](quickstart-install-power-bi-content-pack.md).
* [O que são os relatórios do AD do Azure?](overview-reports.md).
