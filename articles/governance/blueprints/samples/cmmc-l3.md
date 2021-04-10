---
title: Amostra de planta de nível 3 CMMC
description: Visão geral da amostra de planta do CMMC Nível 3. Este exemplo de esquema ajuda os clientes a avaliar controlos específicos.
ms.date: 03/24/2021
ms.topic: sample
ms.openlocfilehash: 950c6064ce8b3d9973ac08e5895a4b6f48e37d6a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572659"
---
# <a name="cmmc-level-3-blueprint-sample"></a>Amostra de planta de nível 3 CMMC

A amostra de planta CMMC Level 3 fornece guarda-carris de governação utilizando [a Política Azure](../../policy/overview.md) que o ajudam a avaliar controlos [específicos de certificação de modelos de maturidade de cibersegurança (CMMC).](https://www.acq.osd.mil/cmmc/index.html) Este projeto ajuda os clientes a implementar um conjunto central de políticas para qualquer arquitetura implantada pelo Azure que deve implementar controlos para o CmMC Nível 3.

## <a name="control-mapping"></a>Mapeamento de controlo

O [mapeamento do controlo da política Azure](../../policy/samples/cmmc-l3.md) fornece detalhes sobre as definições políticas incluídas neste projeto e como estas definições políticas mapeiam para os **controlos** no quadro da CMMC. Quando atribuídos a uma arquitetura, os recursos são avaliados pela Azure Policy para o incumprimento das definições políticas atribuídas. Para obter mais informações, veja [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Implementar

Para implantar a amostra de planta CMMC Nível 3 da Azure Blueprints, devem ser tomadas as seguintes medidas:

> [!div class="checklist"]
> - Criar um esquema novo a partir do exemplo
> - Marcar a cópia do exemplo como **Publicada**
> - Atribuir a cópia do esquema a uma subscrição já existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

### <a name="create-blueprint-from-sample"></a>Criar planta a partir de amostra

Primeiro, crie um esquema novo no ambiente utilizando o exemplo como ponto de partida, para implementar o esquema de exemplo.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Na página **Começar** à esquerda, selecione o botão **Criar** em _Criar um esquema_.

1. Encontre a amostra de planta **CMMC Level 3** em _Outras Amostras_ e selecione **Utilize esta amostra**.

1. Introduza as _Informações Básicas_ do esquema de exemplo:

   - **Nome da planta**: Forneça um nome para a sua cópia da amostra de planta CMMC Level 3.
   - **Localização de definição**: Utilize a elipse e selecione o grupo de gestão para guardar a sua cópia da amostra para.

1. Selecione o separador _Artefactos_, na parte superior da página, ou **Seguinte: Artefactos**, na parte inferior.

1. Reveja a lista de artefactos incluídos na amostra de planta. Muitos dos artefactos têm parâmetros que vamos definir mais tarde. Quando terminar de rever o esquema de exemplo, selecione **Guardar Rascunho**.

### <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

A cópia do esquema de exemplo está agora criada no seu ambiente. Está criada no modo **Rascunho** e tem de ser **Publicada** antes de poder ser atribuída e implementada. A cópia da amostra de planta pode ser personalizada para o seu ambiente e necessidades, mas essa modificação pode movê-la para longe do alinhamento com os controlos CMMC Nível 3.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **Definições de esquema** à esquerda. Utilize os filtros para localizar a cópia do esquema de exemplo e selecione-a.

1. Selecione **Publicar esquema**, na parte superior da página. Na página nova à direita, indique uma **Versão** para a cópia do esquema de exemplo. Esta propriedade é útil se fizer modificações mais tarde. Fornecer **notas de alteração** como "Primeira versão publicada a partir da amostra de planta cmmc nível 3." Em seguida, selecione **Publicar**, na parte inferior da página.

### <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Uma vez publicada com **sucesso** a cópia da amostra de plantas, pode ser atribuída a uma subscrição dentro do grupo de gestão a que foi guardada. É neste passo que são fornecidos os parâmetros que fazem com que cada implementação da cópia do esquema de exemplo seja única.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **Definições de esquema** à esquerda. Utilize os filtros para localizar a cópia do esquema de exemplo e selecione-a.

1. Selecione **Atribuir esquema**, na parte superior da página de definição do esquema.

1. Indique os valores dos parâmetros para a atribuição do esquema:

   - Noções básicas

     - **Subscrições**: Selecione uma ou mais das subscrições que estão no grupo de gestão a que guardou a sua cópia da amostra de planta. Se selecionar mais de uma subscrição, é criada uma atribuição para cada uma mediante a utilização dos parâmetros introduzidos.
     - **Nome da atribuição**: O nome é pré-povoado para si com base no nome da planta.
       Mude-o se necessário ou deixe-o como está.
     - **Localização**: Selecione uma região para a identidade gerida a criar. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, consulte [identidades geridas para recursos Azure.](../../../active-directory/managed-identities-azure-resources/overview.md)
     - **Versão de definição de planta**: Escolha uma versão **publicada** da sua cópia da amostra de planta.

   - Atribuição de bloqueio

     Selecione a definição de bloqueio do esquema no seu ambiente. Para obter mais informações, veja [bloqueio de recurso em esquemas](../concepts/resource-locking.md).

   - Identidade Gerida

     Deixe a opção de identidade gerida _por defeito atribuída._

   - Parâmetros dos artefactos

     Os parâmetros definidos nesta secção aplicam-se ao artefacto no qual são definidos. Estes parâmetros são [parâmetros dinâmicos,](../concepts/parameters.md#dynamic-parameters) uma vez que são definidos durante a atribuição da planta. Para obter uma lista completa ou parâmetros de artefactos e suas descrições, consulte [a tabela de parâmetros do Artefacto](#artifact-parameters-table).

1. Depois de introduzidos todos os parâmetros, selecione **Atribuir**, na parte inferior da página. A atribuição do esquema é criada e a implementação do artefacto inicia-se. A implantação leva cerca de uma hora. Para verificar o estado, abra a atribuição do esquema.

> [!WARNING]
> O serviço Azure Blueprints e os esquemas de exemplo incorporados são **gratuitos**. Os preços dos recursos do Azure são os [preços por produto](https://azure.microsoft.com/pricing/). Utilize a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para prever o custo da execução de recursos implementados por este esquema de exemplo.

### <a name="artifact-parameters-table"></a>Tabela de parâmetros dos artefactos

A tabela seguinte mostra uma lista dos parâmetros dos artefactos de esquema:

|Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|CMMC Nível 3|Atribuição de Política|Inclua servidores ligados ao Arco ao avaliar as políticas de configuração dos hóspedes|Ao selecionar "verdadeiro", concorda em ser carregado mensalmente por máquina ligada ao Arco; para mais informações, visite https://aka.ms/policy-pricing|
|CMMC Nível 3|Atribuição de Política|Lista de utilizadores que devem ser excluídos do grupo de administradores do Windows VM|Uma lista separada de utilizadores que deve ser excluída no grupo local administradores; Ex: Administrador; myUser1; myUser2|
|CMMC Nível 3|Atribuição de Política|Lista de utilizadores que devem ser incluídos no grupo de administradores do Windows VM|Uma lista separada de utilizadores que deve ser incluída no grupo local administradores; Ex: Administrador; myUser1; myUser2|
|CMMC Nível 3|Atribuição de Política|ID do espaço de trabalho do Log Analytics para relatórios de agentes VM|ID (GUID) do espaço de trabalho Log Analytics onde os agentes VMs devem reportar|
|CMMC Nível 3|Atribuição de Política|Nomes de curvas elípticas permitidos|A lista de nomes de curvas permitidos para certificados de criptografia de curva elíptica.|
|CMMC Nível 3|Atribuição de Política|Tipos de chaves permitidos|A lista de tipos-chave permitidos|
|CMMC Nível 3|Atribuição de Política|Permitir a utilização da rede de anfitriões para cápsulas de cluster Kubernetes|Desa esta avaliação se o pod for autorizado a utilizar a rede de anfitriões de outra forma falsa.|
|CMMC Nível 3|Atribuição de Política|Auditar alteração de política de autenticação|Especifica se os eventos de auditoria são gerados quando são feitas alterações à política de autenticação. Esta definição é útil para rastrear alterações no nível de domínio e confiança ao nível da floresta e privilégios que são concedidos a contas ou grupos de utilizadores.|
|CMMC Nível 3|Atribuição de Política|Auditar alteração de política de autorização|Especifica se os eventos de auditoria são gerados para a atribuição e remoção dos direitos dos utilizadores nas políticas de direito do utilizador, alterações na permissão de objetos simbólicos de segurança, alterações de atributos de recursos e alterações da Política de Acesso Central para objetos do sistema de ficheiros.|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Azure Backup deve ser ativado para máquinas virtuais|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Contas de Serviços Cognitivos devem restringir o acesso à rede|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As instâncias geridas pela SQL devem usar chaves geridas pelo cliente para encriptar dados em repouso|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A AZure API para FHIR deve usar uma chave gerida pelo cliente (CMK) para encriptar dados em repouso|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Firewall de aplicação web (WAF) deve ser ativado para o serviço de porta frontal Azure|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As definições de Avaliação de Vulnerabilidade para servidor SQL devem conter um endereço de e-mail para receber relatórios de digitalização|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: O acesso à rede pública deve ser desativado para contas de Serviços Cognitivos|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: O CORS não deve permitir que todos os recursos acedam às suas Apps de Função|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Recomendações de endurecimento da rede adaptativa devem ser aplicadas na internet face a máquinas virtuais|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Deve haver mais de um proprietário atribuído à sua subscrição|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A encriptação do disco deve ser aplicada em máquinas virtuais|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A notificação por e-mail ao proprietário da subscrição para alertas de alta gravidade deve ser ativada|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Cofre-chave deve ter proteção de purga ativada|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Os servidores SQL devem usar chaves geridas pelo cliente para encriptar dados em repouso|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Depuragem remota deve ser desligada para apps de funções|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Azure Defender for Key Vault deve ser ativado|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: O backup geo-redundante deve ser ativado para a Base de Dados Azure para a MariaDB|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: CORS não deve permitir que todos os domínios acedam à sua API para FHIR|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As máquinas windows devem satisfazer os requisitos para "Opções de Segurança - Segurança da Rede"|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As regras allowlist na sua política de controlo de aplicações adaptativas devem ser atualizadas|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Firewall de aplicação web (WAF) deve utilizar o modo especificado para o Gateway de aplicação|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As chaves devem ter datas de validade definidas|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A encriptação transparente de dados nas bases de dados SQL deve ser ativada|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: O perfil de registo do Monitor Azure deve recolher registos para categorias "escrever", "excluir" e "ação"|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A avaliação da vulnerabilidade deve ser ativada em casos geridos do SQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Certifique-se de que a 'versão PHP' é a mais recente, se usada como parte da aplicação API|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: O cofre da chave deve ter a eliminação suave ativada|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Um administrador do Azure Ative Directory deve ser previsto para servidores SQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Apenas devem ser ativadas ligações seguras à sua Cache Azure para Redis|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A encriptação da infraestrutura deve ser ativada para a Base de Dados de Azure para servidores PostgreSQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A solução de proteção endpoint deve ser instalada em conjuntos de escala de máquinas virtuais|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Azure Defender for App Service deve ser ativado|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As máquinas windows devem satisfazer os requisitos para "Políticas de Auditoria do Sistema - Mudança de Políticas"|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As contas dos Serviços Cognitivos devem permitir a encriptação de dados|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: O acesso ao SSH a partir da Internet deve ser bloqueado|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Os discos não ligados devem ser encriptados|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Azure Defender for Storage deve ser ativado|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As contas de armazenamento devem restringir o acesso à rede|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: O CORS não deve permitir que todos os recursos acedam à sua App API|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Implementar proteção avançada de ameaças nas contas de armazenamento|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As variáveis de conta de automação devem ser encriptadas|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Os registos de diagnóstico no IoT Hub devem ser ativados|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A encriptação da infraestrutura deve ser ativada para a Base de Dados Azure para servidores MySQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Deve existir um alerta de registo de atividade para operações de segurança específicas (Microsoft.Security/securitySolutions/delete)|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquinas virtuais devem ser remediadas|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As máquinas windows devem satisfazer os requisitos para "Opções de Segurança - Acesso à Rede"|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A transferência segura para as contas de armazenamento deve ser ativada|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: O Azure Monitor deve recolher registos de atividade de todas as regiões|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Firewall de aplicação web (WAF) deve utilizar o modo especificado para O Serviço de Porta Frontal Azure|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As contas de armazenamento devem ter encriptação de infraestrutura|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Os controlos de aplicação adaptativos para a definição de aplicações seguras devem ser ativados nas suas máquinas|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A cópia de segurança geo-redundante deve ser ativada para a Base de Dados Azure para postgreSQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As máquinas windows devem satisfazer os requisitos para "Opções de Segurança - Controlo de Contas de Utilizador"|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Certifique-se de que 'Versão Java' é a mais recente, se usada como parte da aplicação Web|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Azure Defender para servidores deve ser ativado|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Um máximo de 3 proprietários deve ser designado para a sua subscrição|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As subscrições devem ter um endereço de e-mail de contacto para questões de segurança|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: O acesso público à conta de armazenamento deve ser proibido|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Uma solução de avaliação de vulnerabilidade deve ser ativada nas suas máquinas virtuais|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Azure Defender for Kubernetes deve ser ativado|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Firewall deve ser ativado no Cofre de Chaves|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Firewall de aplicação web (WAF) deve ser ativado para Gateway de aplicação|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: O CORS não deve permitir que todos os recursos acedam às suas Aplicações Web|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Auditar máquinas Windows que permitem reutilizar as 24 palavras-passe anteriores|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Os registos de contentores devem ser encriptados com uma chave gerida pelo cliente (CMK)|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Contas externas com permissões de escrita devem ser removidas da sua subscrição|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: O acesso à rede pública deve ser desativado para servidores flexíveis PostgreSQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As vulnerabilidades nas imagens do Registo de Contentores do Azure devem ser remediadas|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Contas externas com permissões de leitura devem ser removidas da sua subscrição|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Os clusters de tecido de serviço devem ter a propriedade ClusterProtectionLevel definida para EncryptAndSign|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Azure Defender para servidores SQL em máquinas deve ser ativado|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As contas dos Serviços Cognitivos devem permitir a encriptação de dados com a chave gerida pelo cliente|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As contas precodadas devem ser removidas da sua subscrição|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A App de função só deve estar acessível através do HTTPS|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A notificação por e-mail para alertas de alta gravidade deve ser ativada|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A conta de armazenamento deve usar a chave gerida pelo cliente para encriptação|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Certifique-se de que 'Versão Python' é a mais recente, se usada como parte da aplicação Web|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Certifique-se de que a 'versão Python' é a mais recente, se usada como parte da aplicação Function|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Certifique-se de que a 'versão PHP' é a mais recente, se usada como parte da aplicação WEB|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Certifique-se de que a 'versão Python' é a mais recente, se usada como parte da aplicação API|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As chaves devem ser o tipo criptográfico especificado RSA ou CE|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As subscrições do Azure devem ter um perfil de registo para o Registo de Atividades|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Tanto os sistemas operativos como os discos de dados nos clusters de serviço Azure Kubernetes devem ser encriptados por chaves geridas pelo cliente|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Azure Defender para servidores de base de dados Azure SQL deve ser ativado|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A encriptação do Azure Data Explorer em repouso deve usar uma chave gerida pelo cliente|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As chaves que utilizam criptografia RSA devem ter um tamanho mínimo especificado da chave|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A cópia de segurança geo-redundante deve ser ativada para a Base de Dados Azure para o MySQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As cápsulas de cluster Kubernetes só devem utilizar a rede de anfitriões aprovada e a gama portuária|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As atualizações do sistema devem ser instaladas nas suas máquinas|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As máquinas windows devem satisfazer os requisitos para "Políticas de Auditoria do Sistema - Utilização de Privilégios"|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Os empregos do Azure Stream Analytics devem usar chaves geridas pelo cliente para encriptar dados|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Certifique-se de que 'Versão Java' é a mais recente, se usada como parte da app API|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Certifique-se de que 'VERSÃO HTTP' é a mais recente, se usada para executar a aplicação Web|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A versão TLS mais recente deve ser usada na sua App API|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: MFA deve ser ativado contas com permissões de escrita na sua subscrição|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Certifique-se de que 'VERSÃO HTTP' é a mais recente, se usada para executar a aplicação API|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A extensão do Microsoft IaaSAntimalware deve ser implementada nos servidores do Windows|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Certifique-se de que 'Versão Java' é a mais recente, se usada como parte da aplicação Function|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Todas as portas de rede devem ser restringidas em grupos de segurança de rede associados à sua máquina virtual|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: O nível de preços padrão do Centro de Segurança deve ser selecionado|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Auditar máquinas Windows que não restringem o comprimento mínimo da palavra-passe a 14 caracteres|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Auditar o uso das regras personalizadas do RBAC|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A Aplicação Web só deve ser acessível em HTTPS|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A auditoria no servidor SQL deve ser ativada|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: O agente Log Analytics deve ser instalado em máquinas virtuais|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A segurança avançada dos dados deve ser ativada nos seus servidores SQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A segurança avançada dos dados deve ser ativada em SqL Managed Instance|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Role-Based Controlo de Acesso (RBAC) deve ser utilizado nos serviços kubernetes|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As máquinas virtuais devem ter a extensão de Configuração do Hóspede|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Monitorar a falta de proteção de pontos finais no Centro de Segurança Azure|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: O registo de atividade deve ser mantido durante pelo menos um ano|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As portas de gestão das máquinas virtuais devem ser protegidas com controlo de acesso à rede just-in-time|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: O acesso à rede pública deve ser desativado para servidores PostgreSQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Implementar proteção avançada de ameaças para contas DB cosmos|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Os registos de diagnóstico nos Serviços de Aplicações devem ser ativados|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: App API só deve estar acessível em HTTPS|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Deve existir um alerta de registo de atividade para operações administrativas específicas (Microsoft.ClassicNetwork/networkSecurityGroups/delete)|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Deve existir um alerta de registo de atividade para operações administrativas específicas (Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete)|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Deve existir um alerta de registo de atividade para operações administrativas específicas (Microsoft.Network/networkSecurityGroups/delete)|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Deve existir um alerta de registo de atividade para operações administrativas específicas (Microsoft.Network/networkSecurityGroups/securityRules/delete)|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Deve existir um alerta de registo de atividade para operações administrativas específicas (Microsoft.Sql/servers/firewallRules/delete)|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As máquinas virtuais não orientadas para a Internet devem ser protegidas com grupos de segurança de rede|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Auditar máquinas Windows que não têm a definição de complexidade da palavra-passe ativada|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Azure Defender para registos de contentores deve ser ativado|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Os trabalhos da Caixa de Dados Azure devem permitir a dupla encriptação para os dados em repouso no dispositivo|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As atualizações do sistema em conjuntos de escala de máquinas virtuais devem ser instaladas|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: O Microsoft Antimalware for Azure deve ser configurado para atualizar automaticamente as assinaturas de proteção|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Deve existir um alerta de registo de atividade para operações políticas específicas (Microsoft.Autorização/políticaAstribuimentos/eliminação)|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: O acesso à rede pública deve ser desativado para servidores flexíveis MySQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As contas de armazenamento devem permitir o acesso a partir de serviços confiáveis da Microsoft|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Depuragem remota deve ser desligada para aplicações web|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Os certificados que utilizam a criptografia RSA devem ter o tamanho mínimo especificado da chave|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Os registos de contentores não devem permitir o acesso ilimitado à rede|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A ligação SSL da Enforce deve ser ativada para servidores de base de dados PostgreSQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A extensão de configuração do hóspede deve ser implantada em máquinas virtuais Azure com identidade gerida atribuída ao sistema|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: O backup geo-redundante a longo prazo deve ser ativado para as bases de dados Azure SQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: O acesso à rede pública deve ser desativado para servidores MySQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Auditar máquinas Windows que não armazenam palavras-passe usando encriptação reversível|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As máquinas windows devem satisfazer os requisitos para a "Atribuição de Direitos de Utilizador"|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As vulnerabilidades na configuração de segurança nas suas máquinas devem ser remediadas|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Certifique-se de que 'VERSÃO HTTP' é a mais recente, se usada para executar a aplicação 'Fun's'|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: O MFA deve ser ativado em contas com permissões de leitura na sua subscrição|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: O acesso do PDR da Internet deve ser bloqueado|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Auditar máquinas Linux que não têm as permissões de ficheiros passwd definidas para 0644|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As subnetas devem ser associadas a um Grupo de Segurança de Rede|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A ligação SSL da Enforce deve ser ativada para servidores de base de dados MySQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As vulnerabilidades nas configurações de segurança dos contentores devem ser remediadas|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Depuragem remota deve ser desligada para apps da API|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Auditar máquinas Linux que permitem ligações remotas a partir de contas sem senhas|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As contas precodadas com permissões do proprietário devem ser removidas da sua subscrição|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A dupla encriptação deve ser ativada no Azure Data Explorer|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: O agente Log Analytics deve ser instalado em conjuntos de escala de máquinas virtuais|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A versão TLS mais recente deve ser usada na sua Web App|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A encriptação do disco deve ser ativada no Azure Data Explorer|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As máquinas virtuais viradas para a Internet devem ser protegidas com grupos de segurança de rede|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Auditar máquinas Linux que têm contas sem senhas|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Os espaços de trabalho do Azure Synapse devem utilizar chaves geridas pelo cliente para encriptar dados em repouso|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Contas externas com permissões do proprietário devem ser removidas da sua subscrição|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: A versão TLS mais recente deve ser usada na sua App de Função|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Os serviços da Kubernetes devem ser atualizados para uma versão kubernetes não vulnerável|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: Todo o tráfego de Internet deve ser encaminhado através do seu Azure Firewall implantado|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As máquinas Linux devem satisfazer os requisitos para a linha de base de segurança Azure|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: O acesso à rede pública deve ser desativado para servidores MariaDB|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As vulnerabilidades nas bases de dados SQL devem ser remediadas|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Efeito para a política: As chaves que utilizam a criptografia da curva elíptica devem ter os nomes de curva especificados|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC Nível 3|Atribuição de Política|Espaços de nome excluídos da avaliação da política: As cápsulas de agrupamento kubernetes só devem utilizar a rede de anfitriões aprovada e a gama portuária|Lista de espaços de nomes kubernetes para excluir da avaliação de políticas.|
|CMMC Nível 3|Atribuição de Política|Versão java mais recente para Serviços de Aplicações|Versão java suportada mais recente para Serviços de Aplicações|
|CMMC Nível 3|Atribuição de Política|Versão python mais recente para Linux para Serviços de Aplicações|Versão python suportada mais recente para Serviços de Aplicações|
|CMMC Nível 3|Atribuição de Política|Opcional: Lista de imagens VM que apoiaram o Linux OS para adicionar ao âmbito ao auditar a implementação do agente Do Log Analytics|Valor de exemplo: '/subscrições/ <subscriptionId> /resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage'|
|CMMC Nível 3|Atribuição de Política|Opcional: Lista de imagens VM que apoiaram o Windows OS para adicionar ao âmbito ao auditar a implementação do agente Do Log Analytics|Valor de exemplo: '/subscrições/ <subscriptionId> /resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage'|
|CMMC Nível 3|Atribuição de Política|Lista de regiões onde o Observador de Rede deve ser ativado|Auditoria se o Observador de Rede não estiver habilitado para a(s) região(s).|
|CMMC Nível 3|Atribuição de Política|Lista de tipos de recursos que devem ter registos de diagnóstico ativados||
|CMMC Nível 3|Atribuição de Política|Valor máximo na gama portuária de hospedeiro admissível que os pods podem usar no espaço de nome da rede de anfitriões|O valor máximo na gama de portas de hospedeiro admissível que os pods podem usar no espaço de nome da rede de anfitriões.|
|CMMC Nível 3|Atribuição de Política|Tamanho mínimo da chave RSA para chaves|O tamanho mínimo da chave para as teclas RSA.|
|CMMC Nível 3|Atribuição de Política|Certificados mínimos de tamanho das chaves RSA|O tamanho mínimo da chave para certificados RSA.|
|CMMC Nível 3|Atribuição de Política|Versão TLS mínima para servidores web do Windows|Servidores web do Windows com versões TLS mais baixas serão avaliados como não conformes|
|CMMC Nível 3|Atribuição de Política|Valor mínimo na gama portuária de hospedeiro admissível que os pods podem usar no espaço de nome da rede de anfitriões|O valor mínimo na gama de portas de hospedeiro admissível que as cápsulas podem usar no espaço de nome da rede de anfitriões.|
|CMMC Nível 3|Atribuição de Política|Requisito de modo|Modo necessário para todas as políticas waf|
|CMMC Nível 3|Atribuição de Política|Requisito de modo|Modo necessário para todas as políticas waf|
|CMMC Nível 3|Atribuição de Política|Permitiu que os caminhos de anfitrião para volumes pod hostPath para usar|Os caminhos de anfitrião permitiram a utilização de volumes pod hostPath. Forneça uma lista de caminhos vazios para bloquear todos os caminhos do hospedeiro.|
|CMMC Nível 3|Atribuição de Política|Acesso de rede: caminhos de registo remotamente acessíveis|Especifica quais os caminhos de registo acessíveis através da rede, independentemente dos utilizadores ou grupos listados na lista de controlo de acesso (ACL) da chave de `winreg` registo.|
|CMMC Nível 3|Atribuição de Política|Acesso à rede: Caminhos de registo e sub-caminhos acessíveis remotamente|Especifica quais os caminhos de registo e sub-caminhos acessíveis através da rede, independentemente dos utilizadores ou grupos listados na lista de controlo de acesso (ACL) da chave de `winreg` registo.|
|CMMC Nível 3|Atribuição de Política|Acesso de rede: as partilhas que podem ser acedidas anonimamente|Especifica quais as partilhas de rede que podem ser acedidas por utilizadores anónimos. A configuração padrão para esta definição de política tem pouco efeito porque todos os utilizadores têm de ser autenticados antes de poderem aceder a recursos partilhados no servidor.|
|CMMC Nível 3|Atribuição de Política|Segurança da rede: Configurar tipos de encriptação permitidos para Kerberos|Especifica os tipos de encriptação que kerberos podem usar.|
|CMMC Nível 3|Atribuição de Política|Segurança de rede: nível de autenticação do LAN Manager|Especifique qual o protocolo de autenticação de resposta a desafios utilizado para os logons de rede. Esta escolha afeta o nível de protocolo de autenticação utilizado pelos clientes, o nível de segurança da sessão negociado e o nível de autenticação aceite pelos servidores.|
|CMMC Nível 3|Atribuição de Política|Segurança de rede: requisitos de assinatura de cliente LDAP|Especifique o nível de assinatura de dados que é solicitado em nome de clientes que emitem pedidos LDAP BIND.|
|CMMC Nível 3|Atribuição de Política|Segurança de rede: segurança de sessão mínima para os clientes baseados em NTLM SSP (incluindo RPC seguro)|Especifica quais os comportamentos permitidos pelos clientes para aplicações que utilizam o Fornecedor de Suporte de Segurança NTLM (SSP). A Interface SSP (SSPI) é utilizada por aplicações que necessitam de serviços de autenticação. Veja [https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/network-security-minimum-session-security-for-ntlm-ssp-based-including-secure-rpc-servers](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/network-security-minimum-session-security-for-ntlm-ssp-based-including-secure-rpc-servers) mais informações.|
|CMMC Nível 3|Atribuição de Política|Segurança de rede: segurança de sessão mínima para os servidores baseados em NTLM SSP (incluindo RPC seguro)|Especifica quais os comportamentos permitidos pelos servidores para aplicações que utilizam o Fornecedor de Suporte de Segurança NTLM (SSP). A Interface SSP (SSPI) é utilizada por aplicações que necessitam de serviços de autenticação.|
|CMMC Nível 3|Atribuição de Política|Versão PHP mais recente para Serviços de Aplicações|Versão PHP mais recente suportada para Serviços de Aplicações|
|CMMC Nível 3|Atribuição de Política|Período de retenção necessário (dias) para registos de diagnóstico do IoT Hub||
|CMMC Nível 3|Atribuição de Política|Nome do grupo de recursos para O Observador de Rede|Nome do grupo de recursos do NetworkWatcher, como NetworkWatcherRG. Este é o grupo de recursos onde estão localizados os Observadores de Rede.|
|CMMC Nível 3|Atribuição de Política|Definição de auditoria necessária para servidores SQL||
|CMMC Nível 3|Atribuição de Política|SKUs da Caixa de Dados Azure que suportam encriptação dupla baseada em software|A lista de SKUs da Caixa de Dados Azure que suportam encriptação dupla baseada em software|
|CMMC Nível 3|Atribuição de Política|UAC: Modo de aprovação de administrador para a conta de administrador incorporado|Especifica o comportamento do Modo de Aprovação de Administração para a conta de administrador incorporado.|
|CMMC Nível 3|Atribuição de Política|UAC: Comportamento da solicitação de elevação para administradores em Modo de Aprovação de Administrador|Especifica o comportamento da solicitação de elevação para os administradores.|
|CMMC Nível 3|Atribuição de Política|UAC: Detetar instalações de aplicações e pronta para elevação|Especifica o comportamento da deteção da instalação de aplicações para o computador.|
|CMMC Nível 3|Atribuição de Política|UAC: Executar todos os administradores no Modo de Aprovação de Administração|Especifica o comportamento de todas as definições de política do Controlo de Conta de Utilizador (UAC) para o computador.|
|CMMC Nível 3|Atribuição de Política|Utilizador e grupos que podem forçar o encerramento de um sistema remoto|Especifica quais os utilizadores e grupos autorizados a desligar o computador a partir de uma localização remota na rede.|
|CMMC Nível 3|Atribuição de Política|Utilizadores e grupos a quem é negado acesso a este computador a partir da rede|Especifica quais os utilizadores ou grupos que estão explicitamente proibidos de se ligarem ao computador através da rede.|
|CMMC Nível 3|Atribuição de Política|Utilizadores e grupos que são negados início de saúde local|Especifica quais os utilizadores e grupos que não estão explicitamente autorizados a iniciar sessão no computador.|
|CMMC Nível 3|Atribuição de Política|Utilizadores e grupos que são negados a iniciar sessão como um trabalho de lote|Especifica quais os utilizadores e grupos que não estão explicitamente autorizados a iniciar sessão no computador como uma tarefa de lote (isto é, tarefa programada).|
|CMMC Nível 3|Atribuição de Política|Utilizadores e grupos que são negados a iniciar sessão como um serviço|Especifica quais as contas de serviço que explicitamente não estão autorizadas a registar um processo como um serviço.|
|CMMC Nível 3|Atribuição de Política|Utilizadores e grupos que são negados iniciar sessão através de Serviços de Ambiente de Trabalho Remoto|Especifica quais os utilizadores e grupos que não estão explicitamente autorizados a iniciar sessão no computador através de Serviços de Terminal/Cliente de Secretária Remota.|
|CMMC Nível 3|Atribuição de Política|Utilizadores e grupos que podem restaurar ficheiros e diretórios|Especifica quais os utilizadores e grupos autorizados a contornar ficheiros, diretórios, registos e outras permissões de objetos persistentes ao restaurar ficheiros e diretórios com reserva.|
|CMMC Nível 3|Atribuição de Política|Utilizadores e grupos que podem desligar o sistema|Especifica quais os utilizadores e grupos que estão ligados localmente aos computadores do seu ambiente que estão autorizados a desligar o sistema operativo com o comando Shut Down.|
|CMMC Nível 3|Atribuição de Política|Utilizadores ou grupos que podem iniciar sessão local|Especifica quais os utilizadores remotos da rede que podem ligar-se ao computador. Isto não inclui a Ligação remota do ambiente de trabalho.|
|CMMC Nível 3|Atribuição de Política|Utilizadores ou grupos que podem fazer o back up ficheiros e diretórios|Especifica utilizadores e grupos autorizados a contornar permissões de ficheiros e diretórios para fazer o back up do sistema.|
|CMMC Nível 3|Atribuição de Política|Utilizadores ou grupos que podem alterar o tempo do sistema|Especifica quais os utilizadores e grupos autorizados a alterar a hora e a data no relógio interno do computador.|
|CMMC Nível 3|Atribuição de Política|Utilizadores ou grupos que podem alterar o fuso horário|Especifica quais os utilizadores e grupos autorizados a alterar o fuso horário do computador.|
|CMMC Nível 3|Atribuição de Política|Utilizadores ou grupos que podem criar um objeto simbólico|Especifica quais os utilizadores e grupos autorizados a criar um token de acesso, que pode fornecer direitos elevados de acesso a dados sensíveis.|
|CMMC Nível 3|Atribuição de Política|Utilizadores ou grupos que podem iniciar sessão local|Especifica quais os utilizadores ou grupos que podem iniciar sessão interativamente no computador. Os utilizadores que tentem iniciar sessão através da Remote Desktop Connection ou iIS também requerem o direito deste utilizador.|
|CMMC Nível 3|Atribuição de Política|Utilizadores de Ambiente de Trabalho Remoto|Utilizadores ou grupos que podem iniciar sessão através de Serviços remotos de desktop|
|CMMC Nível 3|Atribuição de Política|Utilizadores ou grupos que possam gerir o registo de auditoria e segurança|Especifica utilizadores e grupos autorizados a alterar as opções de auditoria para ficheiros e diretórios e limpar o registo de Segurança.|
|CMMC Nível 3|Atribuição de Política|Utilizadores ou grupos que podem tomar posse de ficheiros ou outros objetos|Especifica quais os utilizadores e grupos autorizados a apropriar-se de ficheiros, pastas, chaves de registo, processos ou fios. Este direito do utilizador ignora quaisquer permissões que estejam em vigor para proteger objetos para dar propriedade ao utilizador especificado.|

## <a name="next-steps"></a>Passos seguintes

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).