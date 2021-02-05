---
title: Desenvolver aplicações seguras no Microsoft Azure
description: Este artigo discute as melhores práticas a ter em conta durante as fases de implementação e verificação do seu projeto de aplicação web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 8e69803f4740a58adb4230bd82cc723221762cb4
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576621"
---
# <a name="develop-secure-applications-on-azure"></a>Desenvolver aplicações seguras no Azure
Neste artigo apresentamos atividades de segurança e controlos a ter em conta quando desenvolve aplicações para a nuvem. Questões e conceitos de segurança a ter em conta durante as fases de implementação e verificação do Ciclo de Vida para o Desenvolvimento da Segurança da Microsoft [(SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) são cobertos. O objetivo é ajudá-lo a definir atividades e serviços Azure que você pode usar para desenvolver uma aplicação mais segura.

Neste artigo, são abrangidas as seguintes fases SDL:

- Implementação
- Verificação

## <a name="implementation"></a>Implementação
O foco da fase de implementação é estabelecer as melhores práticas para a prevenção precoce e detetar e remover as questões de segurança do código.
Assuma que a sua aplicação será usada de formas que não pretendia que fosse usada. Isto ajuda-o a prevenir o uso indevido acidental ou intencional da sua aplicação.

### <a name="perform-code-reviews"></a>Realizar revisões de código

Antes de verificar o código, realize [revisões de código](/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) para aumentar a qualidade global do código e reduzir o risco de criar bugs. Pode utilizar [o Visual Studio](/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) para gerir o processo de revisão de códigos.

### <a name="perform-static-code-analysis"></a>Efetuar a análise de código estático

[A análise de código estático](https://owasp.org/www-community/controls/Static_Code_Analysis) (também conhecida como *análise de código fonte)* é geralmente realizada como parte de uma revisão de código. A análise de código estático refere-se geralmente à execução de ferramentas de análise de código estático para encontrar potenciais vulnerabilidades em código não-funcionando, utilizando técnicas como [verificação de manchas](https://en.wikipedia.org/wiki/Taint_checking) e [análise de fluxo de dados](https://en.wikipedia.org/wiki/Data-flow_analysis).

O Azure Marketplace oferece [ferramentas de desenvolvimento](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) que realizam análises estáticas de código e ajudam nas análises de código.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>Validar e higienizar todas as entradas para a sua aplicação

Trate todas as entradas como falsas para proteger a sua aplicação das vulnerabilidades mais comuns da aplicação web. Dados falsos são um veículo para ataques de injeção. A entrada para a sua aplicação inclui parâmetros no URL, entrada do utilizador, dados da base de dados ou de uma API, e qualquer coisa que seja passada na qual um utilizador possa potencialmente manipular. Uma aplicação deve [validar](https://owasp.org/www-project-proactive-controls/v3/en/c5-validate-inputs) que os dados são sintaticamente e semânticamente válidos antes que a aplicação utilize os dados de qualquer forma (incluindo exibi-lo de volta ao utilizador).

Validar a entrada no início do fluxo de dados para garantir que apenas os dados devidamente formados entram no fluxo de trabalho. Não pretende que os dados por formação permaneçam na sua base de dados ou desencadeiem uma avaria num componente a jusante.

A lista de bloqueios e a lista de admissão são duas abordagens gerais para a validação da sintaxe de entrada:

  - A lista de bloqueios tenta verificar se uma determinada entrada do utilizador não contém conteúdo "conhecido por malicioso".

  - Permite que a lista tente verificar se uma determinada entrada do utilizador corresponde a um conjunto de entradas "bem conhecidas". A lista de licenciamento baseada em caracteres é uma forma de permitir a listagem quando uma aplicação verifica que a entrada do utilizador contém apenas caracteres "bem conhecidos" ou que a entrada corresponde a um formato conhecido.
    Por exemplo, isto pode envolver verificar se um nome de utilizador contém apenas caracteres alfanuméricos ou que contém exatamente dois números.

Permitir a lista é a abordagem preferida para a construção de software seguro.
A lista de bloqueios é propensa a erros porque é impossível pensar numa lista completa de entradas potencialmente más.

Faça este trabalho no servidor, não no lado do cliente (ou no servidor e no lado do cliente).

### <a name="verify-your-applications-outputs"></a>Verifique as saídas da sua aplicação

Qualquer saída que apresente visualmente ou dentro de um documento deve ser sempre codificada e escapada. [A fuga](https://owasp.org/www-community/Injection_Theory#Escaping_.28aka_Output_Encoding.29), também conhecida como *codificação de saídas*, é usada para ajudar a garantir que dados não falsos não são um veículo para um ataque de injeção. A fuga, combinada com a validação de dados, fornece defesas em camadas para aumentar a segurança do sistema como um todo.

A fuga garante que tudo é exibido como *saída.* Escapar também permite ao intérprete saber que os dados não se destinam a ser executados, o que impede que os ataques funcionem. Esta é outra técnica comum de ataque chamada *scripting cross-site* (XSS).

Se estiver a utilizar uma estrutura web a partir de terceiros, pode verificar as suas opções de codificação de saídas em websites utilizando a [folha de batota de prevenção OWASP XSS](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md).

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>Utilize consultas parametrizadas quando contactar a base de dados

Nunca crie uma consulta de base de dados em linha "on the fly" no seu código e envie-a diretamente para a base de dados. O código malicioso inserido na sua aplicação pode potencialmente fazer com que a sua base de dados seja roubada, limpa ou modificada. A sua aplicação também pode ser utilizada para executar comandos de sistema operativo maliciosos no sistema operativo que acolhe a sua base de dados.

Em vez disso, utilize consultas parametrizadas ou procedimentos armazenados. Quando utilizar consultas parametrizadas, pode invocar o procedimento a partir do seu código com segurança e passar-lhe uma corda sem se preocupar que será tratado como parte da declaração de consulta.

### <a name="remove-standard-server-headers"></a>Remover cabeçalhos de servidor padrão

Cabeçalhos como Server, X-Powered-By e X-AspNet-Version revelam informações sobre o servidor e tecnologias subjacentes. Recomendamos que suprima estes cabeçalhos para evitar a recolha de impressões digitais da aplicação.
Consulte [a remoção dos cabeçalhos padrão do servidor nos websites do Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

### <a name="segregate-your-production-data"></a>Segregar os seus dados de produção

Os seus dados de produção, ou dados "reais", não devem ser utilizados para desenvolvimento, teste ou qualquer outro propósito que não seja o pretendido pelo negócio. Um conjunto de dados mascarado[(anonimizado)](https://en.wikipedia.org/wiki/Data_anonymization)deve ser utilizado para todo o desenvolvimento e teste.

Isto significa que menos pessoas têm acesso aos seus dados reais, o que reduz a sua superfície de ataque. Também significa que menos funcionários vêem dados pessoais, o que elimina uma potencial violação da confidencialidade.

### <a name="implement-a-strong-password-policy"></a>Implementar uma política de senha forte

Para se defender contra a força bruta e a adivinhação baseada no dicionário, é necessário implementar uma política de palavra-passe forte para garantir que os utilizadores criem uma senha complexa (por exemplo, 12 caracteres de comprimento mínimo e que exijam caracteres alfanuméricos e especiais).

Pode utilizar um quadro de identidade para criar e impor políticas de palavra-passe. O Azure AD B2C [ajuda-o](../../active-directory-b2c/tutorial-create-user-flows.md#create-a-password-reset-user-flow)na gestão de passwords, fornecendo políticas incorporadas, [redefinição de senha de autosserviço](../../active-directory-b2c/user-flow-self-service-password-reset.md)e muito mais.

Para se defender de ataques a contas padrão, verifique se todas as chaves e senhas são substituíveis e que são geradas ou substituídas após a instalação de recursos.

Se a aplicação tiver de gerar automaticamente senhas, certifique-se de que as palavras-passe geradas são aleatórias e que têm uma elevada entropia.

### <a name="validate-file-uploads"></a>Validar uploads de ficheiros

Se a sua aplicação permitir [uploads de ficheiros,](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)considere as precauções que pode tomar para esta atividade de risco. O primeiro passo em muitos ataques é colocar algum código malicioso num sistema que está a ser atacado. A utilização de um upload de ficheiros ajuda o atacante a conseguir isto. A OWASP oferece soluções para validar um ficheiro para garantir que o ficheiro que está a carregar é seguro.

A proteção antimalware ajuda a identificar e remover vírus, spyware e outros softwares maliciosos. Pode instalar [o Microsoft Antimalware](../fundamentals/antimalware.md) ou a solução de proteção de ponto final de um parceiro da Microsoft[(Trend Micro](https://www.trendmicro.com/azure/), [Broadcom,](https://www.broadcom.com/products) [McAfee,](https://www.mcafee.com/us/products.aspx) [Windows Defender](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)e [Endpoint Protection](/configmgr/protect/deploy-use/endpoint-protection)).

[O Microsoft Antimalware](../fundamentals/antimalware.md) inclui funcionalidades como proteção em tempo real, digitalização programada, remediação de malware, atualizações de assinaturas, atualizações de motores, relatórios de amostras e recolha de eventos de exclusão. Pode integrar soluções de Antimalware e parceiros da Microsoft com [o Azure Security Center](../../security-center/security-center-partner-integration.md) para facilitar a implementação e deteções incorporadas (alertas e incidentes).

### <a name="dont-cache-sensitive-content"></a>Não cache conteúdo sensível

Não cache conteúdo sensível no navegador. Os navegadores podem armazenar informações para o caching e histórico. Os ficheiros em cache são armazenados numa pasta como a pasta De Ficheiros de Internet Temporária, no caso do Internet Explorer. Quando estas páginas são novamente referidas, o navegador exibe as páginas a partir da sua cache. Se forem apresentadas informações sensíveis (endereço, dados do cartão de crédito, número de Segurança Social, nome de utilizador) para o utilizador, as informações podem ser armazenadas na cache do navegador e ser recuperadas examinando a cache do navegador ou simplesmente premindo o botão **Back** do navegador.

## <a name="verification"></a>Verificação
A fase de verificação envolve um esforço abrangente para garantir que o código satisfaça os princípios de segurança e privacidade estabelecidos nas fases anteriores.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>Encontrar e corrigir vulnerabilidades nas dependências da sua aplicação

Você digitaliza a sua aplicação e as suas bibliotecas dependentes para identificar quaisquer componentes vulneráveis conhecidos. Os produtos que estão disponíveis para realizar esta varredura incluem [OWASP Dependency Check,](https://www.owasp.org/index.php/OWASP_Dependency_Check)[Snyk](https://snyk.io/)e [Black Duck.](https://www.blackducksoftware.com/)

A digitalização de vulnerabilidades alimentada pela [Tinfoil Security](https://www.tinfoilsecurity.com/) está disponível para aplicações web do Azure App Service. [A verificação de Segurança De Tinfoil através do App Service](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) oferece aos desenvolvedores e administradores um meio rápido, integrado e económico de descobrir e abordar vulnerabilidades antes que um ator malicioso possa tirar partido delas.

> [!NOTE]
> Também pode [integrar a Tinfoil Security com a Azure AD.](../../active-directory/saas-apps/tinfoil-security-tutorial.md) A integração da Tinfoil Security com a Azure AD proporciona-lhe os seguintes benefícios:
>  - Em Azure AD, você pode controlar quem tem acesso à Segurança De Tinfoil.
>  - Os seus utilizadores podem ser automaticamente inscritos na Tinfoil Security (súmido único) utilizando as suas contas AD Azure.
>  - Pode gerir as suas contas numa única localização central, o portal Azure.

### <a name="test-your-application-in-an-operating-state"></a>Teste a sua aplicação em estado de funcionamento

O Teste dinâmico de segurança de aplicações (DAST) é um processo de teste de uma aplicação num estado operacional para encontrar vulnerabilidades de segurança. As ferramentas DAST analisam os programas enquanto estão a executar para encontrar vulnerabilidades de segurança como corrupção de memória, configuração de servidor inseguro, scripts de sites cruzados, problemas de privilégio do utilizador, injeção de SQL e outras preocupações críticas de segurança.

O DAST é diferente dos testes de segurança da aplicação estática (SAST). As ferramentas SAST analisam código fonte ou versões de código compiladas quando o código não está a ser executado para encontrar falhas de segurança.

Executar o DAST, de preferência com a ajuda de um profissional de segurança (um testador de [penetração](../fundamentals/pen-testing.md) ou avaliador de vulnerabilidade). Se um profissional de segurança não estiver disponível, você pode executar DAST você mesmo com um scanner de procuração web e algum treino. Ligue um scanner DAST logo no início para garantir que não introduz problemas de segurança óbvios no seu código. Consulte o site [da OWASP](https://owasp.org/www-community/Vulnerability_Scanning_Tools) para obter uma lista de scanners de vulnerabilidade de aplicações web.

### <a name="perform-fuzz-testing"></a>Realizar testes de fuzz

Nos [testes de fuzz,](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)induz a falha do programa introduzindo deliberadamente dados mal formados ou aleatórios a uma aplicação. Induzir falha no programa ajuda a revelar potenciais problemas de segurança antes da aplicação ser lançada.

[Deteção de Riscos de Segurança](https://www.microsoft.com/en-us/security-risk-detection/) é o serviço exclusivo de testes de fuzz da Microsoft para encontrar bugs críticos de segurança no software.

### <a name="conduct-attack-surface-review"></a>Realizar revisão de superfície de ataque

Rever a superfície de ataque após a conclusão do código ajuda a garantir que qualquer alteração de design ou implementação a uma aplicação ou sistema foi considerado. Ajuda a garantir que quaisquer novos vetores de ataque que foram criados como resultado das mudanças, incluindo modelos de ameaça, foi revisto e atenuado.

Pode construir uma imagem da superfície de ataque digitalizando a aplicação. A Microsoft oferece uma ferramenta de análise de superfície de ataque chamada [Attack Surface Analyzer](https://www.microsoft.com/download/details.aspx?id=58105). Você pode escolher entre muitas ferramentas ou serviços de pesquisa de vulnerabilidades e testes de dinâmica comercial, incluindo [OWASP Zed Attack Proxy Project](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project), [Arachni,](http://arachni-scanner.com/) [Skipfish,](https://code.google.com/p/skipfish/)e [w3af](http://w3af.sourceforge.net/). Estas ferramentas de digitalização rastream a sua aplicação e mapeiam as partes da aplicação que estão acessíveis por toda a web. Também pode pesquisar no Azure Marketplace ferramentas de [desenvolvimento semelhantes.](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1)

### <a name="perform-security-penetration-testing"></a>Realizar testes de penetração de segurança

Garantir que a sua aplicação está segura é tão importante como testar qualquer outra funcionalidade. Faça [do teste de penetração](../fundamentals/pen-testing.md) uma parte padrão do processo de construção e implantação. Agende testes de segurança regulares e verificação de vulnerabilidades em aplicações implementadas, e monitorize para portas abertas, pontos finais e ataques.

### <a name="run-security-verification-tests"></a>Executar testes de verificação de segurança

[Secure DevOps Kit for Azure](https://azsk.azurewebsites.net/index.html) (AzSK) contém SVTs para múltiplos serviços da plataforma Azure. Você executou estes SVTs periodicamente para garantir que a sua subscrição Azure e os diferentes recursos que compõem a sua aplicação estão em um estado seguro. Também pode automatizar estes testes utilizando a funcionalidade de extensão de integração contínua/implementação contínua (CI/CD) da AzSK, que disponibiliza SVTs como extensão do Visual Studio.

## <a name="next-steps"></a>Passos seguintes
Nos seguintes artigos, recomendamos controlos de segurança e atividades que possam ajudá-lo a conceber e implementar aplicações seguras.

- [Design de aplicações seguras](secure-design.md)
- [Implementar aplicações seguras](secure-deploy.md)