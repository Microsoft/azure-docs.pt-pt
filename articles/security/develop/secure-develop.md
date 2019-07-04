---
title: Desenvolver aplicações seguras no Microsoft Azure
description: Este artigo discute as melhores práticas a considerar durante as fases de implementação e a verificação do seu projeto de aplicativo web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: f68f6c366571b8f0f04f8507606c1a4008ab0405
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443250"
---
# <a name="develop-secure-applications-on-azure"></a>Desenvolver aplicações seguras no Azure
Neste artigo, apresentamos as atividades de segurança e controlos a serem considerados ao desenvolver aplicativos para a nuvem. Perguntas de segurança e conceitos a serem considerados durante as fases de implementação e a verificação da Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) são abordados. O objetivo é ajudar a definir as atividades e serviços do Azure que pode utilizar para desenvolver um aplicativo mais seguro.

As seguintes fases SDL são abordadas neste artigo:

- Implementação
- Verificação

## <a name="implementation"></a>Implementação
O foco da fase de implementação é para estabelecer as práticas recomendadas para prevenção antecipada e detetar e remover os problemas de segurança do código.
Suponha que seu aplicativo será usado de maneiras que não intencionava seja utilizada. Isto ajuda a proteger contra utilização indevida acidental ou intencional de seu aplicativo.

### <a name="perform-code-reviews"></a>Executar revisões de código

Antes de verificar no código, conduzir [revisões de código](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) para aumentar a qualidade geral do código e reduzir o risco de criação de bugs. Pode usar [Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) para gerenciar o processo de revisão de código.

### <a name="perform-static-code-analysis"></a>Efetuar análise de código estático

[Análise de código estático](https://www.owasp.org/index.php/Static_Code_Analysis) (também conhecido como *análise de código de origem*) geralmente é executada como parte de uma revisão de código. Análise de código estático normalmente se refere à execução de código estático para encontrar vulnerabilidades potenciais no código de não execução usando técnicas como as ferramentas de análise [taint verificando](https://en.wikipedia.org/wiki/Taint_checking) e [análise de fluxo de dados](https://en.wikipedia.org/wiki/Data-flow_analysis).

Ofertas do Azure Marketplace [ferramentas de programação](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) que efetuar análise de código estático e ajudá-lo em revisões de código.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>Valide e limpe cada entrada para a sua aplicação

Trate todas as entradas como não fidedigno para proteger a sua aplicação de vulnerabilidades de aplicativos de web mais comuns. Dados não confiáveis são um veículo para ataques de injeção. Entrada para a sua aplicação inclui parâmetros no URL, de entrada do usuário, os dados da base de dados ou a partir de uma API, e tudo o que é passado em que um usuário poderia ser manipuladas. Um aplicativo deve [validar](https://www.owasp.org/index.php/OWASP_Proactive_Controls_2016#4:_Validate_All_Inputs) que os dados são sintaticamente e semanticamente válidos antes do aplicativo usa os dados de qualquer forma (incluindo exibi-la para o usuário).

Valide a entrada no início do fluxo de dados para garantir que apenas os dados corretamente formados aciona o fluxo de trabalho. Não quer dados mal formados persistir no banco de dados ou acionar uma avaria num componente de downstream.

Blacklisting e listas de permissões são duas abordagens gerais para efetuar a validação de entrada de sintaxe:

  - Blacklisting tenta verificar que uma entrada de utilizador especificado não contém conteúdo "conhecido como mal-intencionado".

  - Lista de permissões tenta verificar se uma entrada de utilizador especificado corresponde a um conjunto de entradas de "bem conhecido". Lista de permissões com base no caráter é um formulário de lista de permissões em que um aplicativo verifica que a entrada do usuário contém carateres de apenas "conhecido bom" ou entrada corresponde a um formato conhecido.
    Por exemplo, isto pode envolver a verificar que um nome de utilizador contém apenas carateres alfanuméricos ou que contém exatamente dois números.

Lista de permissões é a melhor abordagem para a criação de um software seguro.
Blacklisting é suscetível a erros porque não é possível pensar numa lista completa de uma entrada potencialmente incorreta.

Realizar esse trabalho no servidor, não no lado do cliente (ou no servidor e no lado do cliente).

### <a name="verify-your-applications-outputs"></a>Certifique-se de saídas da sua aplicação

Quaisquer dados que apresentam visualmente ou dentro de um documento devem sempre ser codificado e caracteres de escape. [Carateres de escape](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29), também conhecido como *codificação de saída*, é utilizado para ajudar a garantir que os dados não confiáveis não não um veículo para um ataque de injeção. Carateres de escape, combinada com a validação de dados, fornece defesas em camadas para aumentar a segurança do sistema como um todo.

Carateres de escape torna-se de que tudo é apresentado como *saída.* Carateres de escape também permite que o interpretador de saber que os dados não se destina a ser executado, e este procedimento impede ataques de trabalho. Essa é outra técnica de ataque comuns chamada *scripts entre sites* (XSS).

Se estiver a utilizar uma estrutura de web de terceiros, pode verificar as suas opções de codificação de saída nos Web sites utilizando o [folha de referência rápida de prevenção de OWASP XSS](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md).

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>Utilizar consultas parametrizadas ao contactar a base de dados

Nunca crie uma consulta de base de dados de inline "num piscar de olhos" em seu código e enviá-lo diretamente para a base de dados. Código malicioso inserido no seu aplicativo poderia potencialmente causar a base de dados ser roubado, eliminados ou modificados. Seu aplicativo também poderia ser usado para executar comandos de sistema de operativo maliciosos no sistema operacional que aloja a base de dados.

Em alternativa, utilize consultas parametrizadas ou procedimentos armazenados. Quando usa consultas parametrizadas, pode invocar com segurança o procedimento a partir do código e passá-lo uma cadeia de caracteres sem se preocupar que será tratado como parte da instrução de consulta.

### <a name="remove-standard-server-headers"></a>Remover os cabeçalhos de servidor padrão

Cabeçalhos, como o servidor, X-com tecnologia-By, e X-AspNet-Version revelar informações sobre o servidor e tecnologias subjacentes. Recomendamos que suprimir esses cabeçalhos para evitar a impressão digital do aplicativo.
Ver [removendo cabeçalhos de servidor padrão nos Web sites do Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

### <a name="segregate-your-production-data"></a>Separar os seus dados de produção

Os dados de produção ou dados "real", não devem ser utilizados para o desenvolvimento, teste ou qualquer outra finalidade que o que a empresa pretendia. Um mascarada ([anónimos](https://en.wikipedia.org/wiki/Data_anonymization)) conjunto de dados deve ser utilizada para todo o desenvolvimento e teste.

Isso significa que menos pessoas têm acesso aos seus dados reais, o que reduz a superfície de ataque. Isso também significa que os funcionários menos ver os dados pessoais, eliminando uma potencial violação no confidencialidade.

### <a name="implement-a-strong-password-policy"></a>Implementar uma política de palavra-passe segura

Se proteger contra força bruta e com base no dicionário adivinhar, deve implementar uma política de palavra-passe segura para se certificar de que os utilizadores criam uma palavra-passe complexa (por exemplo, 12 carateres comprimento mínimo e a necessidade de alfanuméricos e carateres especiais).

Pode usar uma estrutura de identidade para criar e impor políticas de palavra-passe. O Azure AD B2C ajuda-o com a gestão de palavra-passe, fornecendo [políticas incorporadas](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-password-reset-policy), [reposição de palavra-passe self-service](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-sspr)e muito mais.

Para se Defender contra ataques em contas padrão, certifique-se de que todas as chaves e palavras-passe sejam substituíveis e os que forem gerados ou substituídos após instalar recursos.

Se o aplicativo deve gerar automaticamente palavras-passe, certifique-se de que as palavras-passe geradas são aleatórias e se têm entropia elevada.

### <a name="validate-file-uploads"></a>Validar carregamentos de ficheiros

Se seu aplicativo permite [carregamentos de ficheiros](https://www.owasp.org/index.php/Unrestricted_File_Upload), considere as precauções que pode tomar para esta atividade arriscada. O primeiro passo para muitos ataques é obter um código mal-intencionado num sistema que está sob ataque. Usar o carregamento do ficheiro ajuda o invasor conseguir isso. OWASP oferece soluções para validar um ficheiro para garantir que o ficheiro que está a carregar é seguro.

Proteção antimalware ajuda a identificar e remover vírus, spyware e outro software malicioso. Pode instalar [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) ou solução de proteção de ponto final de um parceiro da Microsoft ([Trend Micro](https://www.trendmicro.com/azure/), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [O Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10), e [System Center Endpoint Protection](https://docs.microsoft.com/sccm/protect/deploy-use/endpoint-protection)).

[O Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) inclui recursos como a proteção em tempo real, análise agendada, remediação de software maligno, atualizações de assinatura, atualizações de mecanismos, exemplos de relatórios e recolha de eventos de exclusão. Pode integrar soluções de Antimalware da Microsoft e de parceiros com [Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-partner-integration) para facilitar a implantação e incorporada deteções (alertas e incidentes).

### <a name="dont-cache-sensitive-content"></a>Não colocar em cache conteúdo confidencial

Não colocar em cache conteúdo confidencial no navegador. Browsers podem armazenar informações de colocação em cache e o histórico. Ficheiros em cache são armazenados numa pasta, como a pasta Temporary Internet Files, no caso do Internet Explorer. Quando essas páginas são referidas novamente, o navegador apresenta as páginas da respetiva cache. Se a informações confidenciais (endereço, detalhes do cartão de crédito, número da Previdência Social, nome de utilizador) são apresentadas ao utilizador, as informações podem estar armazenadas na cache do navegador e ser recuperável, examinando o cache do navegador ou, simplesmente pressionando donavegador **Volta** botão.

## <a name="verification"></a>Verificação
A fase de verificação envolve um esforço abrangente para garantir que o código cumpre os princípios de segurança e privacidade que foram estabelecidos nas fases anteriores.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>Encontrar e corrigir vulnerabilidades em dependências das suas aplicações

Analisar seu aplicativo e suas bibliotecas dependentes para identificar quaisquer componentes vulneráveis conhecidos. Os produtos que estão disponíveis para efetuar esta análise incluem [OWASP Dependency Check](https://www.owasp.org/index.php/OWASP_Dependency_Check),[Snyk](https://snyk.io/), e [preto pato](https://www.blackducksoftware.com/).

Análise de vulnerabilidade com tecnologia [Tinfoil Security](https://www.tinfoilsecurity.com/) está disponível para aplicações de Web do serviço de aplicações do Azure. [Análise do tinfoil Security por meio do serviço de aplicações](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) oferece a desenvolvedores e administradores de uma forma rápida, integrada e económica de detetar e resolver vulnerabilidades antes de um ator malicioso pode aproveitá-los.

> [!NOTE]
> Também pode [integrar o Tinfoil Security com o Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/tinfoil-security-tutorial). Integrar o Tinfoil Security com o Azure AD fornece as seguintes vantagens:
>  - No Azure AD, pode controlar quem tem acesso ao Tinfoil Security.
>  - Os utilizadores podem ser automaticamente sessão iniciados no Tinfoil Security (início de sessão único) através das respetivas contas do Azure AD.
>  - Pode gerir as suas contas num local único e central, o portal do Azure.

### <a name="test-your-application-in-an-operating-state"></a>Teste seu aplicativo num Estado de funcionamento

Teste de segurança de aplicativos dinâmicos (DAST) é um processo de testar o aplicativo num Estado de funcionamento para encontrar vulnerabilidades de segurança. Ferramentas DAST analisam programas enquanto eles estão em execução para localizar vulnerabilidades de segurança, como Corrupção de memória, configuração de servidor inseguro, scripts entre sites, problemas de privilégio do usuário, injeção de SQL e outras preocupações de segurança críticas.

DAST é diferente de testes (SAST) de segurança de aplicativo estático. Ferramentas SAST analisar o código-fonte ou compilados versões de código, quando o código não está em execução para localizar falhas de segurança.

Executar DAST, de preferência, com o auxílio de um profissional de segurança (um [testador de penetração](https://docs.microsoft.com/azure/security/azure-security-pen-testing) ou assessor de vulnerabilidade). Se um profissional de segurança não estiver disponível, pode realizar DAST-se com um scanner de proxy da web e algum treinamento. Plug-in de um scanner DAST desde o início para garantir que não introduzem problemas óbvios de segurança no seu código. Consulte a [OWASP](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools) site para obter uma lista de scanners de vulnerabilidade de aplicação web.

### <a name="perform-fuzz-testing"></a>Executar o teste de difusão

Na [teste difuso](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/), induza falha do programa introduzindo deliberadamente dados mal formados ou aleatórios para uma aplicação. Induzir a falha do programa ajuda a revelar possíveis problemas de segurança antes do aplicativo, lançado.

[Deteção de riscos de segurança](https://docs.microsoft.com/security-risk-detection/) é o Microsoft exclusivo teste difuso service para encontrar bugs críticos de segurança no software.

### <a name="conduct-attack-surface-review"></a>Conduzir a revisão da superfície de ataque

Rever a superfície de ataque após a conclusão de código ajuda a garantir que qualquer design ou uma implementação é alterado para um aplicativo ou sistema tem sido considerado. Ajuda a garantir que quaisquer novos vetores de ataque que foram criados como resultado de alterações, incluindo modelos de ameaças, foi revisado e atenuados.

Pode criar uma imagem da superfície de ataque ao verificar a aplicação. A Microsoft oferece uma ferramenta de análise da superfície de ataque chamada [Attack Surface Analyzer](https://www.microsoft.com/download/details.aspx?id=24487). Pode escolher entre muitas teste dinâmica comerciais e ferramentas ou serviços, incluindo a análise de vulnerabilidade [projeto de Proxy de ataque do OWASP zada](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project), [Arachni](http://arachni-scanner.com/), [Skipfish](https://code.google.com/p/skipfish/), e [w3af](http://w3af.sourceforge.net/). Estas ferramentas de verificação pesquisam a sua aplicação e mapeiam as partes do aplicativo que estão acessíveis pela web. Também poderá procurar no Azure Marketplace para semelhante [ferramentas de programação](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1).

### <a name="perform-security-penetration-testing"></a>Executar testes de penetração de segurança

Garantir que seu aplicativo está seguro é tão importante quanto testar todas as outras funcionalidades. Tornar [testes de penetração](https://docs.microsoft.com/azure/security/azure-security-pen-testing) faz parte do processo de compilação e implementação. Agende testes de segurança regulares e análise em aplicações implementadas de vulnerabilidades e monitorizar para abrir portas, pontos finais e ataques.

### <a name="run-security-verification-tests"></a>Executar testes de verificação de segurança

[Secure DevOps Kit para o Azure](https://azsk.azurewebsites.net/index.html) (AzSK) contém SVTs para vários serviços da plataforma do Azure. Execute estas SVTs periodicamente para assegurar que a sua subscrição do Azure e os recursos de diferentes que compõem a sua aplicação estão num estado seguro. Também é possível automatizar esses testes ao utilizar a funcionalidade de extensões de implementação (CI/CD) integração contínua/contínua de AzSK, que disponibiliza SVTs como uma extensão do Visual Studio.

## <a name="next-steps"></a>Passos Seguintes
Nos seguintes artigos, recomendamos que os controlos de segurança e atividades que podem ajudá-lo a projetar e implantar aplicativos seguros.

- [Design de aplicativos seguros](secure-design.md)
- [Implemente aplicações seguras](secure-deploy.md)
