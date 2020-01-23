---
title: Desenvolva aplicativos seguros no Microsoft Azure
description: Este artigo aborda as práticas recomendadas a serem consideradas durante as fases de implementação e verificação do seu projeto de aplicativo Web.
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
ms.openlocfilehash: 9a4f5094837b0c642c4de75180039064de4e40c2
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513988"
---
# <a name="develop-secure-applications-on-azure"></a>Desenvolva aplicativos seguros no Azure
Neste artigo, apresentamos as atividades de segurança e os controles a serem considerados ao desenvolver aplicativos para a nuvem. As perguntas e os conceitos de segurança a serem considerados durante as fases de implementação e verificação do [SDL (Microsoft Security Development Lifecycle)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) são abordados. O objetivo é ajudá-lo a definir atividades e serviços do Azure que você pode usar para desenvolver um aplicativo mais seguro.

As seguintes fases do SDL são abordadas neste artigo:

- Implementação
- Verificação

## <a name="implementation"></a>Implementação
O foco da fase de implementação é estabelecer as práticas recomendadas para a prevenção antecipada e detectar e remover problemas de segurança do código.
Suponha que seu aplicativo será usado de maneiras que você não pretenda usá-lo. Isso ajuda você a se proteger contra o uso indevido acidental ou intencional do seu aplicativo.

### <a name="perform-code-reviews"></a>Executar revisões de código

Antes de fazer o check-in do código, realize [revisões de código](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) para aumentar a qualidade geral do código e reduzir o risco de criar bugs. Você pode usar o [Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) para gerenciar o processo de revisão de código.

### <a name="perform-static-code-analysis"></a>Efetuar a análise de código estático

A [análise de código estático](https://www.owasp.org/index.php/Static_Code_Analysis) (também conhecida como *análise de código-fonte*) geralmente é executada como parte de uma revisão de código. A análise de código estático normalmente refere-se à execução de ferramentas de análise de código estático para encontrar possíveis vulnerabilidades em código não-em execução usando técnicas como [verificação](https://en.wikipedia.org/wiki/Taint_checking) de comparecer e [análise de fluxo de dados](https://en.wikipedia.org/wiki/Data-flow_analysis).

O Azure Marketplace oferece [ferramentas de desenvolvedor](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) que executam análise de código estático e auxiliam nas revisões de código.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>Validar e limpar todas as entradas para seu aplicativo

Trate todas as entradas como não confiáveis para proteger seu aplicativo das vulnerabilidades mais comuns do aplicativo Web. Os dados não confiáveis são um veículo para ataques de injeção. A entrada para o seu aplicativo inclui parâmetros na URL, entrada do usuário, dados do Database ou de uma API e qualquer coisa que seja passada para que um usuário possa manipular. Um aplicativo deve [validar](https://www.owasp.org/index.php/OWASP_Proactive_Controls_2016#4:_Validate_All_Inputs) que os dados são sintaticamente e semanticamente válidos antes que o aplicativo use os dados de qualquer forma (incluindo exibi-los de volta ao usuário).

Valide a entrada no início do fluxo de dados para garantir que apenas os dados formados corretamente insiram o fluxo de trabalho. Você não quer que dados malformados persistam em seu banco de dado ou disparem um mau funcionamento em um componente downstream.

As listas negras e de lista de permissões são duas abordagens gerais para executar a validação da sintaxe de entrada:

  - As tentativas de lista negra para verificar se uma determinada entrada do usuário não contém conteúdo "conhecido como mal-intencionado".

  - As tentativas de lista de permissões para verificar se uma determinada entrada do usuário corresponde a um conjunto de entradas "boas conhecidas". A lista de permissões baseada em caractere é uma forma de lista de permissões em que um aplicativo verifica se a entrada do usuário contém apenas caracteres "corretos" ou se essa entrada corresponde a um formato conhecido.
    Por exemplo, isso pode envolver a verificação de que um nome de usuário contém apenas caracteres alfanuméricos ou que contém exatamente dois números.

A lista de permissões é a abordagem preferida para a criação de software seguro.
A lista de bloqueios está propenso a erros, pois é impossível considerar a possibilidade de se deparar com uma listagem completa de entrada potencialmente inadequada.

Isso funciona no servidor, não no lado do cliente (ou no servidor e no lado do cliente).

### <a name="verify-your-applications-outputs"></a>Verificar as saídas do seu aplicativo

Qualquer saída que você apresente visualmente ou dentro de um documento sempre deve ser codificada e escapada. O [escape](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29), também conhecido como *codificação de saída*, é usado para ajudar a garantir que os dados não confiáveis não sejam um veículo para um ataque de injeção. O escape, combinado com a validação de dados, fornece defesas em camadas para aumentar a segurança do sistema como um todo.

O escape garante que tudo seja exibido como *saída.* A saída também permite que o intérprete saiba que os dados não devem ser executados e isso impede que os ataques funcionem. Essa é outra técnica de ataque comum chamada XSS ( *script entre sites* ).

Se você estiver usando uma estrutura da Web de terceiros, poderá verificar suas opções de codificação de saída em sites usando a folha de consulta de [prevenção de OWASP XSS](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md).

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>Usar consultas parametrizadas ao entrar em contato com o banco de dados

Nunca crie uma consulta de banco de dados embutida "imediatamente" em seu código e envie-a diretamente para o banco de dados. O código mal-intencionado inserido em seu aplicativo poderia fazer com que o banco de dados fosse roubado, apagado ou modificado. Seu aplicativo também pode ser usado para executar comandos do sistema operacional mal-intencionado no sistema operacional que hospeda seu banco de dados.

Em vez disso, use consultas parametrizadas ou procedimentos armazenados. Ao usar consultas parametrizadas, você pode invocar o procedimento de seu código com segurança e passá-lo para uma cadeia de caracteres sem se preocupar que ele será tratado como parte da instrução de consulta.

### <a name="remove-standard-server-headers"></a>Remover cabeçalhos de servidor padrão

Cabeçalhos como servidor, X-powered-by e X-AspNet-Version revelam informações sobre o servidor e as tecnologias subjacentes. É recomendável suprimir esses cabeçalhos para evitar a impressão digital do aplicativo.
Consulte [removendo cabeçalhos de servidor padrão nos sites do Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

### <a name="segregate-your-production-data"></a>Separar os dados de produção

Seus dados de produção ou dados "reais" não devem ser usados para desenvolvimento, teste ou qualquer outra finalidade do que a empresa pretendida. Um conjunto de testes mascarado ([anônimo](https://en.wikipedia.org/wiki/Data_anonymization)) deve ser usado para todo o desenvolvimento e teste.

Isso significa que menos pessoas têm acesso aos dados reais, o que reduz a superfície de ataque. Isso também significa que menos funcionários veem dados pessoais, o que elimina uma possível violação na confidencialidade.

### <a name="implement-a-strong-password-policy"></a>Implementar uma política de senha forte

Para se defender contra a força bruta e a adivinhação baseada em dicionário, você deve implementar uma política de senha forte para garantir que os usuários criem uma senha complexa (por exemplo, comprimento mínimo de 12 caracteres e exigindo caracteres alfanuméricos e especiais).

Você pode usar uma estrutura de identidade para criar e impor políticas de senha. Azure AD B2C ajuda com o gerenciamento de senhas, fornecendo [políticas internas](../../active-directory-b2c/tutorial-create-user-flows.md#create-a-password-reset-user-flow), [redefinição de senha de autoatendimento](../../active-directory-b2c/active-directory-b2c-reference-sspr.md)e muito mais.

Para se defender contra ataques em contas padrão, verifique se todas as chaves e senhas são substituíveis e se elas são geradas ou substituídas após a instalação dos recursos.

Se o aplicativo precisar gerar senhas automaticamente, verifique se as senhas geradas são aleatórias e se elas têm uma entropia alta.

### <a name="validate-file-uploads"></a>Validar carregamentos de arquivo

Se seu aplicativo permitir [uploads de arquivos](https://www.owasp.org/index.php/Unrestricted_File_Upload), considere as precauções que você pode tomar para essa atividade arriscada. A primeira etapa em muitos ataques é obter um código mal-intencionado em um sistema que está sob ataque. O uso de um upload de arquivo ajuda o invasor a fazer isso. O OWASP oferece soluções para validar um arquivo para garantir que o arquivo que você está carregando seja seguro.

A proteção Antimalware ajuda a identificar e remover vírus, spyware e outros softwares mal-intencionados. Você pode instalar o [Microsoft Antimalware](../fundamentals/antimalware.md) ou uma solução de proteção de ponto de extremidade do parceiro da Microsoft ([Trend Micro](https://www.trendmicro.com/azure/), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)e [Endpoint Protection](https://docs.microsoft.com/configmgr/protect/deploy-use/endpoint-protection)).

[O Microsoft Antimalware](../fundamentals/antimalware.md) inclui recursos como proteção em tempo real, verificação agendada, correção de malware, atualizações de assinatura, atualizações de mecanismo, relatórios de amostras e coleta de eventos de exclusão. Você pode integrar o Microsoft antimalware e as soluções de parceiros à [central de segurança do Azure](../../security-center/security-center-partner-integration.md) para facilitar a implantação e detecções internas (alertas e incidentes).

### <a name="dont-cache-sensitive-content"></a>Não armazenar conteúdo confidencial em cache

Não armazene conteúdo confidencial no navegador. Os navegadores podem armazenar informações para cache e histórico. Os arquivos em cache são armazenados em uma pasta como a pasta Temporary Internet Files, no caso do Internet Explorer. Quando essas páginas são referenciadas novamente, o navegador exibe as páginas de seu cache. Se informações confidenciais (endereço, detalhes do cartão de crédito, número do seguro social, nome de usuário) forem exibidas para o usuário, as informações poderão ser armazenadas no cache do navegador e poderão ser recuperadas examinando o cache do navegador ou simplesmente pressionando o botão **voltar** do navegador.

## <a name="verification"></a>Verificação
A fase de verificação envolve um esforço abrangente para garantir que o código atenda às filosofias de segurança e privacidade que foram estabelecidas nas fases anteriores.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>Localizar e corrigir vulnerabilidades em suas dependências de aplicativo

Você examina seu aplicativo e suas bibliotecas dependentes para identificar os componentes vulneráveis conhecidos. Os produtos que estão disponíveis para realizar essa verificação incluem [verificação de dependência OWASP](https://www.owasp.org/index.php/OWASP_Dependency_Check),[Snyk](https://snyk.io/)e [pato preto](https://www.blackducksoftware.com/).

A verificação de vulnerabilidade da plataforma de [tinfoil Security](https://www.tinfoilsecurity.com/) está disponível para aplicativos Web do Azure app Service. A [verificação de tinfoil Security por meio do serviço de aplicativo](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) oferece aos desenvolvedores e administradores um meio rápido, integrado e econômico de descobrir e endereçar vulnerabilidades antes que um ator mal-intencionado possa tirar proveito delas.

> [!NOTE]
> Você também pode [integrar o tinfoil Security ao Azure ad](../../active-directory/saas-apps/tinfoil-security-tutorial.md). A integração do tinfoil Security ao Azure AD oferece os seguintes benefícios:
>  - No Azure AD, você pode controlar quem tem acesso ao tinfoil Security.
>  - Seus usuários podem ser automaticamente conectados ao tinfoil Security (logon único) usando suas contas do Azure AD.
>  - Você pode gerenciar suas contas em um único local central, o portal do Azure.

### <a name="test-your-application-in-an-operating-state"></a>Testar seu aplicativo em um estado operacional

O DAST (teste de segurança do aplicativo dinâmico) é um processo de teste de um aplicativo em um estado operacional para encontrar vulnerabilidades de segurança. As ferramentas de DAST analisam programas enquanto estão em execução para encontrar vulnerabilidades de segurança, como corrupção de memória, configuração de servidor inseguro, script entre sites, problemas de privilégio de usuário, injeção de SQL e outras preocupações de segurança críticas.

O DAST é diferente do SAST (teste de segurança do aplicativo) estático. As ferramentas SAST analisam o código-fonte ou as versões compiladas do código quando o código não está em execução para encontrar falhas de segurança.

Execute o DAST, preferencialmente com a assistência de um profissional de segurança (um [testador de penetração](../fundamentals/pen-testing.md) ou uma assessor de vulnerabilidade). Se um profissional de segurança não estiver disponível, você poderá executar o DAST por conta própria com um scanner de proxy da Web e algum treinamento. Conecte um scanner DAST no início para garantir que você não introduza problemas de segurança óbvios em seu código. Consulte o site do [OWASP](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools) para obter uma lista de scanners de vulnerabilidade de aplicativos Web.

### <a name="perform-fuzz-testing"></a>Executar teste de difusão

No [teste de fuzzing](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/), você induzi a falha do programa deliberadamente introduzindo dados incorretos ou aleatórios em um aplicativo. A falha do programa induzir ajuda a revelar possíveis problemas de segurança antes do lançamento do aplicativo.

A [detecção de riscos de segurança](https://docs.microsoft.com/security-risk-detection/) é o serviço de teste de difusão exclusivo da Microsoft para localizar bugs críticos de segurança no software.

### <a name="conduct-attack-surface-review"></a>Conduzir revisão da superfície de ataque

Examinar a superfície de ataque após a conclusão do código ajuda a garantir que qualquer alteração de design ou implementação em um aplicativo ou sistema tenha sido considerada. Ele ajuda a garantir que todos os novos vetores de ataque criados como resultado das alterações, incluindo modelos de ameaça, tenham sido revisados e atenuados.

Você pode criar uma imagem da superfície de ataque examinando o aplicativo. A Microsoft oferece uma ferramenta de análise da superfície de ataque chamada [analisador de superfície de ataque](https://www.microsoft.com/download/details.aspx?id=24487). Você pode escolher entre muitos serviços e ferramentas de verificação de vulnerabilidade e teste dinâmico comercial, incluindo o [projeto de proxy de ataque OWASP zada](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project), [Arachni](http://arachni-scanner.com/), [skipfish](https://code.google.com/p/skipfish/)e [w3af](http://w3af.sourceforge.net/). Essas ferramentas de verificação rastreiam seu aplicativo e mapeiam as partes do aplicativo que podem ser acessadas pela Web. Você também pode pesquisar no Azure Marketplace por [ferramentas de desenvolvedor](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1)semelhantes.

### <a name="perform-security-penetration-testing"></a>Executar teste de penetração de segurança

Garantir que seu aplicativo seja seguro é tão importante quanto testar qualquer outra funcionalidade. Faça um [teste de penetração](../fundamentals/pen-testing.md) de uma parte padrão do processo de compilação e implantação. Agende testes de segurança regulares e a verificação de vulnerabilidade em aplicativos implantados e monitore para portas abertas, pontos de extremidade e ataques.

### <a name="run-security-verification-tests"></a>Executar testes de verificação de segurança

O [Secure DevOps Kit para Azure](https://azsk.azurewebsites.net/index.html) (AzSK) contém SVTs para vários serviços da plataforma Azure. Você executa esses SVTs periodicamente para garantir que sua assinatura do Azure e os diferentes recursos que compõem seu aplicativo estejam em um estado seguro. Você também pode automatizar esses testes usando o recurso de extensões CI/CD (integração contínua/implantação contínua) do AzSK, que torna o SVTs disponível como uma extensão do Visual Studio.

## <a name="next-steps"></a>Passos seguintes
Nos artigos a seguir, recomendamos os controles de segurança e as atividades que podem ajudá-lo a projetar e implantar aplicativos seguros.

- [Criar aplicativos seguros](secure-design.md)
- [Implantar aplicativos seguros](secure-deploy.md)
