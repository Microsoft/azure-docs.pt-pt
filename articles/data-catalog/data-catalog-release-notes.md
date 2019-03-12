---
title: Notas de versão do catálogo de dados do Azure
description: Notas de versão do catálogo de dados do Azure.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: 3aca9c49-45a4-4352-92e6-bd25ee3eacf7
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 60c5b7b55e417a5703010ea34cf75dcb20146c37
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531684"
---
# <a name="azure-data-catalog-release-notes"></a>Notas de versão do catálogo de dados do Azure
## <a name="notes-for-the-november-20-2015-release-of-azure-data-catalog"></a>Notas para a 20 de Novembro de 2015 da versão do catálogo de dados do Azure
### <a name="opening-data-sources-in-power-bi-desktop"></a>Origens de dados de abertura no Power BI Desktop
Ao utilizar a opção "Abrir no Power BI Desktop" a partir da **catálogo de dados do Azure** portal, os utilizadores poderão encontrar um dos dois problemas na aplicação Power BI Desktop:

* É apresentada uma caixa de diálogo com o título "Não é possível ao documento aberto"
* O aplicativo de área de trabalho do Power BI é aberto, mas o arquivo parece vazio

Para cada situação, o problema pode ser resolvido ao transferir e instalar a versão mais recente do Power BI Desktop a partir [PowerBI.com](https://powerbi.com).

## <a name="notes-for-the-november-13-2015-release-of-azure-data-catalog"></a>Notas para a 13 de Novembro de 2015 da versão do catálogo de dados do Azure
### <a name="registering-and-connecting-to-teradata"></a>Registar e ligar a Teradata
Ao ligar a origens de dados de Teradata utilizadores devem ter instalado o controlador ODBC de Teradata correto que corresponde ao número de bits (32 bits ou 64 bits) do software a ser utilizado.

Esta data de lançamento do ADC, o mais recente [controlador ODBC de Teradata para windows (versão 15.10)](https://downloads.teradata.com/download/connectivity/odbc-driver/windows) é compatível com o Office 2013, mas não com o Office 2016.

## <a name="notes-for-the-july-13-2015-release-of-azure-data-catalog"></a>Notas para a 13 de Julho de 2015 da versão do catálogo de dados do Azure
### <a name="registering-and-connecting-to-oracle-database"></a>Registar e ligar a base de dados Oracle
Ao ligar a origens de dados de base de dados Oracle os utilizadores devem ser instalados os controladores de Oracle corretos, que correspondem o número de bits (32 bits ou 64 bits) do software a ser utilizado.

* Ao registar origens de dados Oracle num computador com o Windows de 32 bits, serão utilizados os controladores de Oracle de 32 bits
* Ao registar origens de dados Oracle num computador com o Windows de 64 bits, serão utilizados os controladores de Oracle de 64 bits
* Ao ligar a origens de dados do Oracle com o Excel num computador a executar a versão de 32 bits do Microsoft Office, incluindo no Windows de 64 bits, os controladores de Oracle de 32 bits serão utilizados
* Ao ligar a origens de dados do Oracle com o Excel num computador a executar a versão de 64 bits do Microsoft Office, serão utilizados os controladores de Oracle de 64 bits

### <a name="registering-and-connecting-to-sql-server-reporting-services"></a>Registar e ligar ao SQL Server Reporting Services
Suporte para origens de dados do SQL Server Reporting Services (SSRS) está limitado a apenas os servidores de modo nativo. Suporte para SSRS no modo SharePoint será adicionado numa versão posterior.

### <a name="opening-data-assets-in-excel"></a>Recursos de dados de abertura no Excel
Ao abrir os recursos de dados no Microsoft Excel a partir da **catálogo de dados do Azure** portal, os utilizadores podem ser-lhe-um **aviso de segurança do Microsoft Excel** caixa de diálogo. Este é o padrão, comportamento esperado e os utilizadores podem selecionar **ativar** para continuar.

Para obter mais informações, consulte [ative ou desative alertas de segurança sobre ligações e os ficheiros a partir de Web sites suspeitos](https://support.office.com/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE).

### <a name="proxy-and-policy-configuration-and-data-source-registration"></a>Proxy e a política de registo da origem de dados e configuração
Os utilizadores poderão encontrar uma situação onde podem iniciar sessão no portal do catálogo de dados do Azure, mas quando tentarem iniciar sessão para a ferramenta de registo da origem de dados encontram uma mensagem de erro que impede que iniciar sessão.

Existem duas causas possíveis para este comportamento de problema:

**Fazer com que 1: Configuração de serviços de Federação de diretório Active Directory** a ferramenta de registo da origem de dados utiliza a autenticação de formulários para validar os inícios de sessão do utilizador no Active Directory. Para o início de sessão bem-sucedidos, autenticação de formulários tem de estar ativada na política de autenticação Global por um administrador do Active Directory.

Em algumas situações, esse comportamento de erro pode ocorrer apenas quando o utilizador está na rede da empresa, ou apenas quando o usuário se conectar de fora da rede da empresa. A política de autenticação Global permite que os métodos de autenticação a ser ativada em separado para ligações extranet e de intranet. Se a autenticação de formulários não está ativada para a rede a partir do qual o usuário se conectar, poderão ocorrer erros de início de sessão.

Para obter mais informações, consulte [configurar políticas de autenticação](https://technet.microsoft.com/library/dn486781.aspx).

**Causa 2: Configuração de proxy de rede** se a rede empresarial utiliza um servidor proxy, a ferramenta de registo poderá não conseguir ligar ao Azure Active Directory através do proxy. Os utilizadores possam garantir que a ferramenta de registo ao editar o ficheiro de configuração da ferramenta, adicionar esta secção para o ficheiro:

      <system.net>
        <defaultProxy useDefaultCredentials="true" enabled="true">
          <proxy usesystemdefault="True"
                          proxyaddress="http://<your corporate network proxy url>"
                          bypassonlocal="True"/>
        </defaultProxy>
      </system.net>


Para localizar o ficheiro de RegistrationTool.exe.config, inicie a ferramenta de registo e, em seguida, abra o utilitário do Gestor de tarefas do Windows. No separador Detalhes do Gestor de tarefas, clique com o botão direito no RegistrationTool.exe e escolha Abrir localização do ficheiro a partir do menu pop-up.
