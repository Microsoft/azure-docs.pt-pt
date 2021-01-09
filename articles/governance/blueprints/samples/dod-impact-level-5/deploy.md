---
title: Amostra de planta do DoD Impact Level 5
description: Implementar etapas para a amostra de planta do DoD Impact Level 5, incluindo detalhes do parâmetro do artefacto da planta.
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: 6136136eef5d405ae0849e5ce8c8faede138fb00
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98034905"
---
# <a name="deploy-the-dod-impact-level-5-blueprint-sample"></a>Implementar a amostra de planta do DoD Impact Level 5

Para implantar a amostra de planta do Azure Blueprints Department of Defense Impact Level 5 (DoD IL5), devem ser tomadas as seguintes medidas:

> [!div class="checklist"]
> - Criar um esquema novo a partir do exemplo
> - Marcar a cópia do exemplo como **Publicada**
> - Atribuir a cópia do esquema a uma subscrição já existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar planta a partir de amostra

Primeiro, crie um esquema novo no ambiente utilizando o exemplo como ponto de partida, para implementar o esquema de exemplo.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Na página **Começar** à esquerda, selecione o botão **Criar** em _Criar um esquema_.

1. Encontre a amostra de planta **do DoD Impact Level 5** em _outras amostras_ e selecione **Utilize esta amostra**.

1. Introduza as _Informações Básicas_ do esquema de exemplo:

   - **Nome da planta**: Forneça um nome para a sua cópia da amostra de planta DoD Impact Level 5.
   - **Localização de definição**: Utilize a elipse e selecione o grupo de gestão para guardar a sua cópia da amostra para.

1. Selecione o separador _Artefactos_, na parte superior da página, ou **Seguinte: Artefactos**, na parte inferior.

1. Reveja a lista de artefactos que compõem o esquema de exemplo. Muitos dos artefactos têm parâmetros que vamos definir mais tarde. Quando terminar de rever o esquema de exemplo, selecione **Guardar Rascunho**.

## <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

A cópia do esquema de exemplo está agora criada no seu ambiente. Está criada no modo **Rascunho** e tem de ser **Publicada** antes de poder ser atribuída e implementada. A cópia da amostra de planta pode ser personalizada para o seu ambiente e necessidades, mas essa modificação pode movê-la para longe do alinhamento com os controlos DoD Impact Level 5.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **Definições de esquema** à esquerda. Utilize os filtros para localizar a cópia do esquema de exemplo e selecione-a.

1. Selecione **Publicar esquema**, na parte superior da página. Na página nova à direita, indique uma **Versão** para a cópia do esquema de exemplo. Esta propriedade é útil se fizer modificações mais tarde. Fornecer **notas de alteração** como "Primeira versão publicada a partir da amostra de planta DoD IL5." Em seguida, selecione **Publicar**, na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Uma vez publicada com **sucesso** a cópia da amostra de plantas, pode ser atribuída a uma subscrição dentro do grupo de gestão a que foi guardada. É neste passo que são fornecidos os parâmetros que fazem com que cada implementação da cópia do esquema de exemplo seja única.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **Definições de esquema** à esquerda. Utilize os filtros para localizar a cópia do esquema de exemplo e selecione-a.

1. Selecione **Atribuir esquema**, na parte superior da página de definição do esquema.

1. Indique os valores dos parâmetros para a atribuição do esquema:

   - Noções básicas

     - **Subscrições**: Selecione uma ou mais das subscrições que estão no grupo de gestão a que guardou a sua cópia da amostra de planta. Se selecionar mais de uma subscrição, é criada uma atribuição para cada uma mediante a utilização dos parâmetros introduzidos.
     - **Nome da atribuição**: O nome é pré-povoado para si com base no nome da planta.
       Mude-o se necessário ou deixe-o como está.
     - **Localização**: Selecione uma região para a identidade gerida a criar. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, consulte [identidades geridas para recursos Azure.](../../../../active-directory/managed-identities-azure-resources/overview.md)
     - **Versão de definição de planta**: Escolha uma versão **publicada** da sua cópia da amostra de planta.

   - Atribuição de bloqueio

     Selecione a definição de bloqueio do esquema no seu ambiente. Para obter mais informações, veja [bloqueio de recurso em esquemas](../../concepts/resource-locking.md).

   - Identidade Gerida

     Deixe a opção de identidade gerida _por defeito atribuída._

   - Parâmetros dos artefactos

     Os parâmetros definidos nesta secção aplicam-se ao artefacto no qual são definidos. Estes parâmetros são [parâmetros dinâmicos,](../../concepts/parameters.md#dynamic-parameters) uma vez que são definidos durante a atribuição da planta. Para obter uma lista completa ou parâmetros de artefactos e suas descrições, consulte [a tabela de parâmetros do Artefacto](#artifact-parameters-table).

1. Depois de introduzidos todos os parâmetros, selecione **Atribuir**, na parte inferior da página. A atribuição do esquema é criada e a implementação do artefacto inicia-se. A implantação leva cerca de uma hora. Para verificar o estado, abra a atribuição do esquema.

> [!WARNING]
> O serviço Azure Blueprints e os esquemas de exemplo incorporados são **gratuitos**. Os preços dos recursos do Azure são os [preços por produto](https://azure.microsoft.com/pricing/). Utilize a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para prever o custo da execução de recursos implementados por este esquema de exemplo.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros dos artefactos

A tabela seguinte mostra uma lista dos parâmetros dos artefactos de esquema:

|Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|Impacto DoD Nível 5|Atribuição de Política|Lista de utilizadores que devem ser incluídos no grupo de administradores do Windows VM|Uma lista separada de utilizadores que deve ser incluída no grupo local administradores; Ex: Administrador; myUser1; myUser2|
|Impacto DoD Nível 5|Atribuição de Política|Lista de utilizadores excluídos do grupo de administradores do Windows VM|Uma lista separada de utilizadores que deve ser excluída no grupo local administradores; Ex: Administrador; myUser1; myUser2|
|Impacto DoD Nível 5|Atribuição de Política|Lista de tipos de recursos que devem ter registos de diagnóstico ativados||
|Impacto DoD Nível 5|Atribuição de Política|ID do espaço de trabalho do Log Analytics para relatórios de agentes VM|ID (GUID) do espaço de trabalho Log Analytics onde os agentes VMs devem reportar|
|Impacto DoD Nível 5|Atribuição de Política|Lista de regiões onde o Observador de Rede deve ser ativado|Para ver uma lista completa de regiões use Get-AzLocation,|
|Impacto DoD Nível 5|Atribuição de Política|Versão TLS mínima para servidores web do Windows|A versão mínima do protocolo TLS que deve ser ativada nos servidores web do Windows|
|Impacto DoD Nível 5|Atribuição de Política|Versão PHP mais recente|Versão PHP mais recente suportada para Serviços de Aplicações|
|Impacto DoD Nível 5|Atribuição de Política|Última versão java|Versão java suportada mais recente para Serviços de Aplicações|
|Impacto DoD Nível 5|Atribuição de Política|Versão mais recente do Windows Python|Versão python suportada mais recente para Serviços de Aplicações|
|Impacto DoD Nível 5|Atribuição de Política|Última versão Linux Python|Versão python suportada mais recente para Serviços de Aplicações|
|Impacto DoD Nível 5|Atribuição de Política|Opcional: Lista de imagens do Windows VM que suportam o agente Log Analytics para adicionar ao âmbito de auditoria|Uma lista de imagens separadas por pontos de costura; Ex: /subscrições/ <subscriptionId> /resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage|
|Impacto DoD Nível 5|Atribuição de Política|Opcional: Lista de imagens Linux VM que suportam o agente Log Analytics para adicionar ao âmbito de auditoria|Uma lista de imagens separadas por pontos de costura; Ex: /subscrições/ <subscriptionId> /resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Deve haver mais de um proprietário atribuído à sua subscrição|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: A encriptação do disco deve ser aplicada em máquinas virtuais|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: A notificação por e-mail ao proprietário da subscrição para alertas de alta gravidade deve ser ativada|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Depuragem remota deve ser desligada para apps de funções|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Certifique-se de que a versão '.NET Framework' é a mais recente, se usada como parte da App de Função|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: A encriptação transparente de dados nas bases de dados SQL deve ser ativada|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: A avaliação da vulnerabilidade deve ser ativada nas suas instâncias geridas pelo SQL|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Certifique-se de que a 'versão PHP' é a mais recente, se usada como parte da aplicação Api|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Um administrador do Azure Ative Directory deve ser previsto para servidores SQL|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Apenas devem ser ativadas ligações seguras à sua Cache Redis|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: A solução de proteção endpoint deve ser instalada em conjuntos de escala de máquinas virtuais|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Auditoria ao acesso ilimitado da rede às contas de armazenamento|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: As definições avançadas de segurança de dados para a instância gerida pelo SQL devem conter um endereço de e-mail para receber alertas de segurança|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquinas virtuais devem ser remediadas|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: A transferência segura para as contas de armazenamento deve ser ativada|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Os controlos de aplicações adaptativos devem ser ativados em máquinas virtuais|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: A cópia de segurança geo-redundante deve ser ativada para a Base de Dados Azure para postgreSQL|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Certifique-se de que 'Versão Java' é a mais recente, se usada como parte da aplicação Web|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Um máximo de 3 proprietários deve ser designado para a sua subscrição|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Um endereço de e-mail de contacto de segurança deve ser fornecido para a sua subscrição|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: O CORS não deve permitir que todos os recursos acedam às suas Aplicações Web|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Contas externas com permissões de escrita devem ser removidas da sua subscrição|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Contas externas com permissões de leitura devem ser removidas da sua subscrição|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: As contas precodadas devem ser removidas da sua subscrição|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: A App de função só deve estar acessível através do HTTPS|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Certifique-se de que 'Versão Python' é a mais recente, se usada como parte da aplicação Web|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Certifique-se de que a 'versão Python' é a mais recente, se usada como parte da aplicação Function|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Certifique-se de que a 'versão PHP' é a mais recente, se usada como parte da aplicação WEB|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Certifique-se de que a 'versão Python' é a mais recente, se usada como parte da aplicação Api|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: As vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: A cópia de segurança geo-redundante deve ser ativada para a Base de Dados Azure para o MySQL|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Certifique-se de que a versão '.NET Framework' é a mais recente, se usada como parte da aplicação Web|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: As atualizações do sistema devem ser instaladas nas suas máquinas|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Certifique-se de que 'Versão Java' é a mais recente, se usada como parte da app Api|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Certifique-se de que 'VERSÃO HTTP' é a mais recente, se usada para executar a aplicação Web|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: A versão TLS mais recente deve ser usada na sua App API|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: MFA deve ser ativado contas com permissões de escrita na sua subscrição|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: As definições avançadas de segurança de dados para o servidor SQL devem conter um endereço de e-mail para receber alertas de segurança|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Certifique-se de que 'VERSÃO HTTP' é a mais recente, se usada para executar a aplicação Api|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: A extensão do Microsoft IaaSAntimalware deve ser implementada nos servidores do Windows|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Certifique-se de que 'Versão Java' é a mais recente, se usada como parte da aplicação Function|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: O acesso através da Internet face ao ponto final deve ser restringido|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: O nível de preços padrão do Centro de Segurança deve ser selecionado|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Auditar o uso das regras personalizadas do RBAC|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: A Aplicação Web só deve ser acessível em HTTPS|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: A auditoria no servidor SQL deve ser ativada|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: O agente Log Analytics deve ser instalado em máquinas virtuais|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: A Norma de Proteção DDoS deve ser ativada|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Certifique-se de que a 'versão PHP' é a mais recente, se usada como parte da aplicação Função|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: A segurança avançada dos dados deve ser ativada nos seus servidores SQL|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: A segurança avançada dos dados deve ser ativada nas suas instâncias geridas pelo SQL|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: As notificações por e-mail aos administradores e proprietários de subscrições devem ser ativadas em definições avançadas de segurança de dados geridas por SQL|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Monitorar a falta de proteção de pontos finais no Centro de Segurança Azure|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: O controlo do acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Um número de telefone de contacto de segurança deve ser fornecido para a sua subscrição|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Os clusters de tecidos de serviço só devem utilizar o Azure Ative Directory para a autenticação do cliente|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: App API só deve estar acessível em HTTPS|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Os tipos avançados de proteção contra ameaças devem ser definidos como 'All' em definições de segurança de dados avançadas de exemplos DE SQL|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: O armazenamento geo-redundante deve ser ativado para contas de armazenamento|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Certifique-se de que a versão ".NET Framework" é a mais recente, se usada como parte da aplicação API|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: As atualizações do sistema em conjuntos de escala de máquinas virtuais devem ser instaladas|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: As notificações por e-mail aos administradores e proprietários de subscrições devem ser ativadas nas definições avançadas de segurança de dados do servidor SQL|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Depuragem remota deve ser desligada para aplicações web|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: O backup geo-redundante a longo prazo deve ser ativado para as bases de dados Azure SQL|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: As vulnerabilidades na configuração de segurança nas suas máquinas devem ser remediadas|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Certifique-se de que 'VERSÃO HTTP' é a mais recente, se usada para executar a aplicação 'Fun's'|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: O MFA deve ser ativado em contas com permissões de leitura na sua subscrição|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Os tipos avançados de proteção contra ameaças devem ser definidos como 'All' nas definições avançadas de segurança de dados do servidor SQL|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: As vulnerabilidades nas configurações de segurança dos contentores devem ser remediadas|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Depuragem remota deve ser desligada para apps da API|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: As contas precodadas com permissões do proprietário devem ser removidas da sua subscrição|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: O agente Log Analytics deve ser instalado em conjuntos de escala de máquinas virtuais|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: A versão TLS mais recente deve ser usada na sua Web App|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: Contas externas com permissões do proprietário devem ser removidas da sua subscrição|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: A versão TLS mais recente deve ser usada na sua App de Função|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: [Pré-visualização]: Os serviços da Kubernetes devem ser atualizados para uma versão kubernetes não vulnerável|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Impacto DoD Nível 5|Atribuição de Política|Efeito para a política: As vulnerabilidades nas bases de dados SQL devem ser remediadas|Efeito político Azure para esta política; para mais informações sobre efeitos, visite https://aka.ms/policyeffects|

## <a name="next-steps"></a>Passos seguintes

Agora que reviu os passos para implementar a amostra de planta do DoD Impact Level 5, visite os seguintes artigos para saber sobre a planta e o mapeamento de controlo:

> [!div class="nextstepaction"]
> [Projeto do DoD Impact Level 5 - Visão geral](./index.md) 
>  [Projeto do DoD Impact Level 5 - Controlo de mapeamento](./control-mapping.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).