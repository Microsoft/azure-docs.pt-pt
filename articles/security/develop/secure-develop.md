---
title: Desenvolver aplicações seguras no Microsoft Azure
description: Este artigo discute as melhores práticas a considerar durante as fases de implementação e verificação do seu projeto de aplicação web.
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
ms.openlocfilehash: 9d98660230e0ab9f4edcd9a7af8a3797106dd17a
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255647"
---
# <a name="develop-secure-applications-on-azure"></a>Desenvolver aplicações seguras no Azure
Neste artigo apresentamos atividades e controlos de segurança a considerar quando desenvolve aplicações para a nuvem. Questões e conceitos de segurança a considerar durante as fases de implementação e verificação do Ciclo de Vida de Desenvolvimento de Segurança da Microsoft [(SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) são abordados. O objetivo é ajudá-lo a definir atividades e serviços Azure que você pode usar para desenvolver uma aplicação mais segura.

As seguintes fases SDL são abrangidas neste artigo:

- Implementação
- Verificação

## <a name="implementation"></a>Implementação
O foco da fase de implementação é estabelecer as melhores práticas para a prevenção precoce e detetar e remover problemas de segurança do código.
Assuma que a sua aplicação será usada de forma sucinta que não pretendia que fosse usada. Isto ajuda-o a prevenir-se contra o uso indevido acidental ou intencional da sua aplicação.

### <a name="perform-code-reviews"></a>Realizar revisões de código

Antes de verificar o código, procedia [a revisões](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) de códigos para aumentar a qualidade global do código e reduzir o risco de criar bugs. Pode utilizar o [Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) para gerir o processo de revisão de códigos.

### <a name="perform-static-code-analysis"></a>Realizar análise de código estático

[A análise de código estático](https://www.owasp.org/index.php/Static_Code_Analysis) (também conhecida como análise de *código fonte)* é geralmente realizada como parte de uma revisão de código. A análise de código estático geralmente refere-se a ferramentas de análise de código estáticos para encontrar potenciais vulnerabilidades no código não corrente, utilizando técnicas como [a verificação de manchas](https://en.wikipedia.org/wiki/Taint_checking) e análise de fluxo de [dados](https://en.wikipedia.org/wiki/Data-flow_analysis).

O Azure Marketplace oferece ferramentas para [programar](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) que realizam análises estáticas de código e ajudam com avaliações de código.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>Valide e sanitize cada entrada para a sua aplicação

Trate todas as inputs como não fidedignas para proteger a sua aplicação das vulnerabilidades mais comuns da aplicação web. Dados não confiáveis são um veículo para ataques de injeção. A entrada para a sua aplicação inclui parâmetros no URL, entrada do utilizador, dados da base de dados ou de uma API, e qualquer coisa que seja passada na que um utilizador possa potencialmente manipular. Uma aplicação deve [validar](https://www.owasp.org/index.php/OWASP_Proactive_Controls_2016#4:_Validate_All_Inputs) que os dados são sintáticae e semânticamente válidos antes de a aplicação utilizar os dados de qualquer forma (incluindo exibi-los de volta ao utilizador).

Validar a entrada no início do fluxo de dados para garantir que apenas os dados devidamente formados entram no fluxo de trabalho. Não pretende que os dados mal formados persistam na sua base de dados ou desencadeiem uma avaria num componente a jusante.

Lista negra e whitelisting são duas abordagens gerais para a validação da sintaxe de entrada:

  - A lista negra tenta verificar se uma determinada entrada de utilizador não contém conteúdo "conhecido como malicioso".

  - A Whitelisting tenta verificar se uma determinada entrada de utilizador corresponde a um conjunto de inputs "bem conhecidos". Whitelisting baseado em caracteres é uma forma de whitelisting onde uma aplicação verifica que a entrada do utilizador contém apenas caracteres "conhecidos bem" ou que a entrada corresponde a um formato conhecido.
    Por exemplo, isto pode envolver verificar se um nome de utilizador contém apenas caracteres alfanuméricos ou que contém exatamente dois números.

Whitelisting é a abordagem preferida para construir software seguro.
A lista negra é propensa a erros porque é impossível pensar numa lista completa de entrada potencialmente má.

Faça este trabalho no servidor, não no lado do cliente (ou no servidor e no lado do cliente).

### <a name="verify-your-applications-outputs"></a>Verifique as saídas da sua aplicação

Qualquer saída que apresentar visualmente ou dentro de um documento deve ser sempre codificada e escapada. [A fuga](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29), também conhecida como codificação de *saídas*, é usada para ajudar a garantir que os dados não fidedignos não são um veículo para um ataque de injeção. A fuga, combinada com a validação de dados, fornece defesas em camadas para aumentar a segurança do sistema como um todo.

A fuga garante que tudo é exibido como *saída.* A fuga também permite ao intérprete saber que os dados não se destinam a ser executados, o que impede que os ataques funcionem. Esta é outra técnica comum de ataque chamada *scripting cross-site* (XSS).

Se estiver a utilizar um quadro web de terceiros, pode verificar as suas opções de codificação de saída nos websites utilizando a folha de batota de [prevenção OWASP XSS](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md).

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>Utilize consultas parametrizadas quando contactar a base de dados

Nunca crie uma consulta de base de dados inline "on the fly" no seu código e envie-a diretamente para a base de dados. O código malicioso inserido na sua aplicação pode potencialmente fazer com que a sua base de dados seja roubada, limpa da dotada ou modificada. A sua aplicação também pode ser utilizada para executar comandos de sistemaoperativo maliciosos no sistema operativo que acolhe a sua base de dados.

Em vez disso, utilize consultas parametrizadas ou procedimentos armazenados. Quando utilizar consultas parametrizadas, pode invocar o procedimento a partir do seu código com segurança e passar-lhe uma corda sem se preocupar que seja tratado como parte da declaração de consulta.

### <a name="remove-standard-server-headers"></a>Remover os cabeçalhos padrão do servidor

Cabeçalhos como Server, X-Powered-By e X-AspNet-Version revelam informações sobre o servidor e tecnologias subjacentes. Recomendamos que suprima estes cabeçalhos para evitar a recolha de impressões digitais na aplicação.
Consulte [a remoção dos cabeçalhos padrão do servidor nos websites do Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

### <a name="segregate-your-production-data"></a>Segregar os seus dados de produção

Os seus dados de produção, ou dados "reais", não devem ser utilizados para desenvolvimento, teste ou qualquer outro fim que não o pretendido pelo negócio. Um conjunto de dados mascarado[(anoonymizado](https://en.wikipedia.org/wiki/Data_anonymization)) deve ser utilizado para todos os desenvolvimentos e testes.

Isto significa que menos pessoas têm acesso aos seus dados reais, o que reduz a sua superfície de ataque. Significa também que menos funcionários vêem dados pessoais, o que elimina uma potencial violação da confidencialidade.

### <a name="implement-a-strong-password-policy"></a>Implementar uma política de senha forte

Para se defender contra a força bruta e a adivinhação baseada no dicionário, deve implementar uma forte política de palavra-passe para garantir que os utilizadores criem uma senha complexa (por exemplo, 12 caracteres comprimento mínimo e que exijacaracteres alfanuméricos e especiais).

Pode utilizar um quadro de identidade para criar e impor políticas de senha. O Azure AD B2C ajuda-o na gestão de passwords, fornecendo [políticas incorporadas,](../../active-directory-b2c/tutorial-create-user-flows.md#create-a-password-reset-user-flow)reposição de [senha de autosserviço,](../../active-directory-b2c/user-flow-self-service-password-reset.md)e muito mais.

Para se defender de ataques a contas predefinidas, verifique se todas as chaves e senhas são substituíveis e que são geradas ou substituídas após a instalação de recursos.

Se a aplicação tiver de gerar automaticamente palavras-passe, certifique-se de que as palavras-passe geradas são aleatórias e que têm uma alta entropia.

### <a name="validate-file-uploads"></a>Validar uploads de ficheiros

Se a sua aplicação permitir uploads de [ficheiros,](https://www.owasp.org/index.php/Unrestricted_File_Upload)considere as precauções que pode tomar para esta atividade de risco. O primeiro passo em muitos ataques é colocar algum código malicioso num sistema que está a ser atacado. Usar um upload de ficheiro ajuda o intruso a conseguir isto. OOWASP oferece soluções para validar um ficheiro para garantir que o ficheiro que está a carregar é seguro.

A proteção antimalware ajuda a identificar e remover vírus, spyware e outros softwares maliciosos. Pode instalar o [Microsoft Antimalware](../fundamentals/antimalware.md) ou a solução de proteção de pontofinal do parceiro Microsoft ([Trend Micro,](https://www.trendmicro.com/azure/) [Broadcom,](https://www.broadcom.com/products) [McAfee,](https://www.mcafee.com/us/products.aspx) [Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)e [Endpoint Protection](https://docs.microsoft.com/configmgr/protect/deploy-use/endpoint-protection)).

O [Microsoft Antimalware](../fundamentals/antimalware.md) inclui funcionalidades como proteção em tempo real, digitalização programada, reparação de malware, atualizações de assinaturas, atualizações do motor, relatórios de amostras e recolha de eventos de exclusão. Pode integrar soluções de Antimalware da Microsoft e parceiros com [o Azure Security Center](../../security-center/security-center-partner-integration.md) para facilitar a implementação e deteções incorporadas (alertas e incidentes).

### <a name="dont-cache-sensitive-content"></a>Não cache conteúdo sensível

Não cache conteúdo sensível no navegador. Os navegadores podem armazenar informações para o cache e histórico. Os ficheiros em cache são armazenados numa pasta como a pasta Ficheiros de Internet Temporário, no caso do Internet Explorer. Quando estas páginas são novamente referidas, o navegador exibe as páginas a partir da sua cache. Se forem apresentadas informações sensíveis (endereço, dados do cartão de crédito, número de Segurança Social, nome de utilizador) ao utilizador, a informação poderá ser armazenada na cache do navegador e ser recuperada examinando a cache do navegador ou bastando premir o botão **Back** do navegador.

## <a name="verification"></a>Verificação
A fase de verificação envolve um esforço abrangente para garantir que o código satisfaz os princípios de segurança e privacidade estabelecidos nas fases anteriores.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>Encontre e corrija vulnerabilidades nas dependências da sua aplicação

Você digitaliza a sua aplicação e suas bibliotecas dependentes para identificar quaisquer componentes vulneráveis conhecidos. Os produtos que estão disponíveis para realizar esta verificação incluem [OWASP Dependency Check,](https://www.owasp.org/index.php/OWASP_Dependency_Check)[Snyk](https://snyk.io/)e [Black Duck](https://www.blackducksoftware.com/).

A verificação de vulnerabilidades alimentada pela [Tinfoil Security](https://www.tinfoilsecurity.com/) está disponível para aplicações web do Serviço de Aplicações Azure. A pesquisa de [Segurança Tinfoil através do App Service](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) oferece aos programadores e administradores um meio rápido, integrado e económico de descobrir e abordar vulnerabilidades antes que um ator malicioso possa aproveitar-se delas.

> [!NOTE]
> Também pode [integrar a Segurança De Tinfoil com a Azure AD.](../../active-directory/saas-apps/tinfoil-security-tutorial.md) Integrar a Segurança Tinfoil com a Azure AD proporciona-lhe os seguintes benefícios:
>  - Em Azure AD, você pode controlar quem tem acesso à Segurança Tinfoil.
>  - Os seus utilizadores podem ser automaticamente inscritos na Tinfoil Security (única investida) utilizando as suas contas Azure AD.
>  - Você pode gerir suas contas em um único local central, o portal Azure.

### <a name="test-your-application-in-an-operating-state"></a>Teste a sua aplicação em estado de funcionamento

O teste dinâmico de segurança da aplicação (DAST) é um processo de teste de uma aplicação em estado operativo para encontrar vulnerabilidades de segurança. As ferramentas DAST analisam programas enquanto estão a executar para encontrar vulnerabilidades de segurança, tais como corrupção de memória, configuração insegura do servidor, scripts transporitais, problemas de privilégio do utilizador, injeção de SQL e outras preocupações críticas de segurança.

O DAST é diferente dos testes estáticos de segurança da aplicação (SAST). As ferramentas SAST analisam código fonte ou compilaram versões de código quando o código não está a ser executado para encontrar falhas de segurança.

Executar AST, de preferência com a ajuda de um profissional de segurança (um [testede penetração](../fundamentals/pen-testing.md) ou avaliador de vulnerabilidades). Se um profissional de segurança não estiver disponível, pode realizar as DAST com um scanner de procuração web e algum treino. Ligue um scanner DAST mais cedo para garantir que não introduzproblema problemas de segurança óbvios no seu código. Consulte o site [oWASP](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools) para obter uma lista de scanners de vulnerabilidade de aplicações web.

### <a name="perform-fuzz-testing"></a>Realizar testes de fuzz

Nos [testes de difusão,](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)induz-se a falha do programa introduzindo deliberadamente dados mal formados ou aleatórios numa aplicação. Induzir falha no programa ajuda a revelar potenciais problemas de segurança antes da aplicação ser lançada.

[Security Risk Detection](https://docs.microsoft.com/security-risk-detection/) é o serviço exclusivo de teste de fuzz da Microsoft para encontrar bugs críticos de segurança no software.

### <a name="conduct-attack-surface-review"></a>Conduzir revisão da superfície do ataque

Rever a superfície de ataque após a conclusão do código ajuda a garantir que qualquer alteração de conceção ou implementação a uma aplicação ou sistema foi considerada. Ajuda a garantir que quaisquer novos vetores de ataque que foram criados como resultado das mudanças, incluindo modelos de ameaça, foram revistos e atenuados.

Pode construir uma imagem da superfície de ataque digitalizando a aplicação. A Microsoft oferece uma ferramenta de análise de superfície de ataque chamada [Attack Surface Analyzer](https://www.microsoft.com/download/details.aspx?id=24487). Você pode escolher entre muitas ferramentas ou serviços de pesquisa de vulnerabilidades e testes de vulnerabilidade comercial, incluindo [OWASP Zed Attack Proxy Project](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project), [Arachni,](http://arachni-scanner.com/) [Skipfish](https://code.google.com/p/skipfish/)e [w3af](http://w3af.sourceforge.net/). Estas ferramentas de digitalização rastejam a sua aplicação e mapeiam as partes da aplicação que são acessíveis através da web. Também pode pesquisar no Mercado Azure ferramentas de [desenvolvimento semelhantes.](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1)

### <a name="perform-security-penetration-testing"></a>Realizar testes de penetração de segurança

Garantir que a sua aplicação é segura é tão importante quanto testar qualquer outra funcionalidade. Faça dos testes de [penetração](../fundamentals/pen-testing.md) uma parte padrão do processo de construção e implementação. Agende testes regulares de segurança e verificação de vulnerabilidades em aplicações implementadas e monitorize para portas abertas, pontos finais e ataques.

### <a name="run-security-verification-tests"></a>Executar testes de verificação de segurança

[O Secure DevOps Kit for Azure](https://azsk.azurewebsites.net/index.html) (AzSK) contém SVTs para vários serviços da plataforma Azure. Executa estes SVTs periodicamente para garantir que a sua subscrição Azure e os diferentes recursos que compõem a sua aplicação estão num estado seguro. Também pode automatizar estes testes utilizando a funcionalidade de extensões de integração contínua/implantação contínua (CI/CD) da AzSK, que disponibiliza SVTs como extensão do Estúdio Visual.

## <a name="next-steps"></a>Passos seguintes
Nos seguintes artigos, recomendamos controlos de segurança e atividades que podem ajudá-lo a projetar e implementar aplicações seguras.

- [Aplicações seguras de design](secure-design.md)
- [Implementar aplicações seguras](secure-deploy.md)
