---
title: Amostra de projeto CIS Microsoft Azure Foundations Benchmark v1.3.0
description: Visão geral da amostra de projeto benchmark das Fundações CIS Microsoft Azure v1.3.0. Este exemplo de esquema ajuda os clientes a avaliar controlos específicos.
ms.date: 03/11/2021
ms.topic: sample
ms.openlocfilehash: 7030b4e9e9ddd3d5bbf1a5a7deaf1e1aa241b38e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566001"
---
# <a name="cis-microsoft-azure-foundations-benchmark-v130-blueprint-sample"></a>Amostra de projeto CIS Microsoft Azure Foundations Benchmark v1.3.0

A amostra de benchmark v1.3.0 da CIS Microsoft Foundations fornece linhas de segurança de governação utilizando a [política Azure](../../policy/overview.md) que o ajudam a avaliar recomendações específicas do CIS Microsoft Azure Foundations Benchmark v1.3.0. Este projeto ajuda os clientes a implementar um conjunto central de políticas para qualquer arquitetura implantada pelo Azure que deve implementar recomendações de Benchmark v1.3.0 da CIS Microsoft Azure Foundations.

## <a name="recommendation-mapping"></a>Mapeamento das recomendações

O [mapeamento da recomendação da Política Azure](../../policy/samples/cis-azure-1-3-0.md) fornece detalhes sobre as definições de política incluídas neste projeto e como estas definições de política mapeiam as **recomendações** no CIS Microsoft Azure Foundations Benchmark v1.3.0. Quando atribuídos a uma arquitetura, os recursos são avaliados pela Azure Policy para o incumprimento das definições políticas atribuídas. Para obter mais informações, veja [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Implementar

Para implementar a amostra de projeto benchmark das Fundações Azure da Azure Blueprints CIS Microsoft Azure, devem ser tomadas as seguintes medidas:

> [!div class="checklist"]
> - Criar um esquema novo a partir do exemplo
> - Marcar a cópia do exemplo como **Publicada**
> - Atribuir a cópia do esquema a uma subscrição já existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

### <a name="create-blueprint-from-sample"></a>Criar planta a partir de amostra

Primeiro, crie um esquema novo no ambiente utilizando o exemplo como ponto de partida, para implementar o esquema de exemplo.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Na página **Começar** à esquerda, selecione o botão **Criar** em _Criar um esquema_.

1. Encontre a amostra **de benchmark v1.3.0 das Fundações CIS Microsoft Azure em** _outras amostras_ e selecione **Utilize esta amostra**.

1. Introduza as _Informações Básicas_ do esquema de exemplo:

   - **Nome da planta**: Forneça um nome para a sua cópia da amostra de projeto benchmark da CIS Microsoft Azure Foundations.
   - **Localização de definição**: Utilize a elipse e selecione o grupo de gestão para guardar a sua cópia da amostra para.

1. Selecione o separador _Artefactos_, na parte superior da página, ou **Seguinte: Artefactos**, na parte inferior.

1. Reveja a lista de artefactos incluídos na amostra de planta. Muitos dos artefactos têm parâmetros que vamos definir mais tarde. Quando terminar de rever o esquema de exemplo, selecione **Guardar Rascunho**.

### <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

A cópia do esquema de exemplo está agora criada no seu ambiente. Está criada no modo **Rascunho** e tem de ser **Publicada** antes de poder ser atribuída e implementada. A cópia da amostra de planta pode ser personalizada para o seu ambiente e necessidades, mas essa modificação pode afastá-la do alinhamento com as recomendações cis Microsoft Azure Foundations Benchmark v1.3.0.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **Definições de esquema** à esquerda. Utilize os filtros para localizar a cópia do esquema de exemplo e selecione-a.

1. Selecione **Publicar esquema**, na parte superior da página. Na página nova à direita, indique uma **Versão** para a cópia do esquema de exemplo. Esta propriedade é útil se fizer modificações mais tarde. Fornecer **notas de alteração** como "Primeira versão publicada a partir da amostra de projeto benchmark da CIS Microsoft Azure Foundations." Em seguida, selecione **Publicar**, na parte inferior da página.

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
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Lista de extensões de máquinas virtuais que são aprovadas para utilização|Uma lista separada de extensões de máquinas virtuais; para ver uma lista completa de extensões, use o comando Azure PowerShell Get-AzVMExtensionImage|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: As instâncias geridas pela SQL devem usar chaves geridas pelo cliente para encriptar dados em repouso|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: As definições de Avaliação de Vulnerabilidade para servidor SQL devem conter um endereço de e-mail para receber relatórios de digitalização|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Os registos de diagnóstico na Azure Data Lake Store devem ser ativados|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: A encriptação do disco deve ser aplicada em máquinas virtuais|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Cofre-chave deve ter proteção de purga ativada|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Garanta que a app API tem "Certificados de Cliente (Certificados de cliente incoming)" definidos para 'On'|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Os servidores SQL devem usar chaves geridas pelo cliente para encriptar dados em repouso|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: A identidade gerida deve ser usada na sua App de Função|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Azure Defender for Key Vault deve ser ativado|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: As funções de proprietário de subscrição personalizada não devem existir|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: As chaves devem ter datas de validade definidas|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: A encriptação transparente de dados nas bases de dados SQL deve ser ativada|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: A avaliação da vulnerabilidade deve ser ativada em casos geridos do SQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Certifique-se de que a 'versão PHP' é a mais recente, se usada como parte da aplicação API|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Um administrador do Azure Ative Directory deve ser previsto para servidores SQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Azure Defender for App Service deve ser ativado|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: As contas de armazenamento devem restringir o acesso à rede utilizando regras de rede virtuais|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: A identidade gerida deve ser usada na sua Web App|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: O acesso ao SSH a partir da Internet deve ser bloqueado|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Os discos não ligados devem ser encriptados|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Azure Defender for Storage deve ser ativado|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: As contas de armazenamento devem restringir o acesso à rede|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Os registos de diagnóstico em Aplicações Lógicas devem ser ativados|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Os registos de diagnóstico no IoT Hub devem ser ativados|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: O FTPS só deve ser exigido na sua App de Função|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Deve existir um alerta de registo de atividade para operações de segurança específicas (Microsoft.Security/securitySolutions/delete)|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Deve existir um alerta de registo de atividade para operações de segurança específicas (Microsoft.Security/securitySolutions/write)|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: A transferência segura para as contas de armazenamento deve ser ativada|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Os registos de diagnóstico nas contas do Lote devem ser ativados|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: O provisionamento automático do agente Log Analytics deve ser ativado na sua subscrição|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Certifique-se de que 'Versão Java' é a mais recente, se usada como parte da aplicação Web|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: FTPS deve ser necessário na sua Web App|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Azure Defender para servidores deve ser ativado|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: As subscrições devem ter um endereço de e-mail de contacto para questões de segurança|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: O acesso público à conta de armazenamento deve ser proibido|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Azure Defender for Kubernetes deve ser ativado|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: O estrangulamento de ligação deve ser ativado para servidores de base de dados PostgreSQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Garanta que a aplicação WEB tem "Certificados de Cliente (Certificados de cliente incoming)" definidos para 'On'|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Contas externas com permissões de escrita devem ser removidas da sua subscrição|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Contas externas com permissões de leitura devem ser removidas da sua subscrição|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Azure Defender para servidores SQL em máquinas deve ser ativado|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: A notificação por e-mail para alertas de alta gravidade deve ser ativada|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: A conta de armazenamento deve usar a chave gerida pelo cliente para encriptação|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Certifique-se de que 'Versão Python' é a mais recente, se usada como parte da aplicação Web|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Certifique-se de que a 'versão Python' é a mais recente, se usada como parte da aplicação Function|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Certifique-se de que a 'versão PHP' é a mais recente, se usada como parte da aplicação WEB|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Certifique-se de que a 'versão Python' é a mais recente, se usada como parte da aplicação API|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Os registos de diagnóstico em conjuntos de escala de máquina virtual devem ser ativados|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Azure Defender para servidores de base de dados Azure SQL deve ser ativado|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Os registos de diagnóstico no Centro de Eventos devem ser ativados|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: As atualizações do sistema devem ser instaladas nas suas máquinas|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Certifique-se de que 'Versão Java' é a mais recente, se usada como parte da app API|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Os servidores SQL devem ser configurados com 90 dias de retenção de auditoria ou superior.|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Certifique-se de que 'VERSÃO HTTP' é a mais recente, se usada para executar a aplicação Web|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: A versão TLS mais recente deve ser usada na sua App API|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: MFA deve ser ativado contas com permissões de escrita na sua subscrição|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: A autenticação deve ser ativada na sua aplicação web|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Segredos devem ter datas de validade definidas|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Certifique-se de que 'VERSÃO HTTP' é a mais recente, se usada para executar a aplicação API|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: O FTPS só deve ser exigido na sua App API|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Certifique-se de que 'Versão Java' é a mais recente, se usada como parte da aplicação Function|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: A Aplicação Web só deve ser acessível em HTTPS|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: A auditoria no servidor SQL deve ser ativada|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: A segurança avançada dos dados deve ser ativada nos seus servidores SQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: A segurança avançada dos dados deve ser ativada em SqL Managed Instance|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Role-Based Controlo de Acesso (RBAC) deve ser utilizado nos serviços kubernetes|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Monitorar a falta de proteção de pontos finais no Centro de Segurança Azure|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Os registos de diagnóstico nos serviços de pesquisa devem ser ativados|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Os registos de diagnóstico nos Serviços de Aplicações devem ser ativados|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Deve existir um alerta de registo de atividade para operações administrativas específicas (Microsoft.Network/networkSecurityGroups/delete)|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Deve existir um alerta de registo de atividade para operações administrativas específicas (Microsoft.Network/networkSecurityGroups/securityRules/delete)|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Deve existir um alerta de registo de atividade para operações administrativas específicas (Microsoft.Network/networkSecurityGroups/securityRules/write)|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Deve existir um alerta de registo de atividade para operações administrativas específicas (Microsoft.Network/networkSecurityGroups/write)|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Deve existir um alerta de registo de atividade para operações administrativas específicas (Microsoft.Sql/servers/firewallRules/delete)|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Deve existir um alerta de registo de atividade para operações administrativas específicas (Microsoft.Sql/servers/firewallRules/write)|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Só devem ser instaladas extensões VM aprovadas|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Azure Defender para registos de contentores deve ser ativado|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Identidade gerida deve ser usada na sua App API|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: A autenticação deve ser ativada na sua aplicação API|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Deve existir um alerta de registo de atividade para operações políticas específicas (Microsoft.Autorização/políticaAstribuimentos/eliminação)|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Deve existir um alerta de registo de atividade para operações políticas específicas (Microsoft.Autorização/políticaAssinsignments/write)|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: A autenticação deve ser ativada na sua aplicação Função|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Os registos de diagnóstico no Data Lake Analytics devem ser ativados|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: As contas de armazenamento devem permitir o acesso a partir de serviços confiáveis da Microsoft|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Os registos de diagnóstico no Cofre-Chave devem ser ativados|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: A ligação SSL da Enforce deve ser ativada para servidores de base de dados PostgreSQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Certifique-se de que 'VERSÃO HTTP' é a mais recente, se usada para executar a aplicação 'Fun's'|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: O MFA deve ser ativado em contas com permissões de leitura na sua subscrição|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: O acesso do PDR da Internet deve ser bloqueado|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: A ligação SSL da Enforce deve ser ativada para servidores de base de dados MySQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Garantir a aplicação 'Cliente Certificates 'Client Certificates)' definidos para 'On'|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Os pontos de verificação de registo devem ser ativados para servidores de base de dados PostgreSQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: As ligações de registo devem ser ativadas para servidores de base de dados PostgreSQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: As desconexões devem ser registadas para servidores de base de dados PostgreSQL.|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: A versão TLS mais recente deve ser usada na sua Web App|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Contas externas com permissões do proprietário devem ser removidas da sua subscrição|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Os registos de diagnóstico no Service Bus devem ser ativados|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: Os registos de diagnóstico no Azure Stream Analytics devem ser ativados|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: A versão TLS mais recente deve ser usada na sua App de Função|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Efeito para a política: A conta de armazenamento que contenha o contentor com registos de atividade deve ser encriptada com BYOK|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Inclua clusters AKS ao auditar se os registos de diagnóstico definidos em escala de máquina virtual estiverem ativados||
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Versão java mais recente para Serviços de Aplicações|Versão java suportada mais recente para Serviços de Aplicações|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Versão python mais recente para Linux para Serviços de Aplicações|Versão python suportada mais recente para Serviços de Aplicações|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Lista de regiões onde o Observador de Rede deve ser ativado|Para ver uma lista completa de regiões, executar o comando PowerShell Get-AzLocation|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Versão PHP mais recente para Serviços de Aplicações|Versão PHP mais recente suportada para Serviços de Aplicações|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Período de retenção necessário (dias) para registos de recursos|Para mais informações sobre registos de recursos, visite [https://aka.ms/resourcelogs](../../../azure-monitor/essentials/resource-logs.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Nome do grupo de recursos para O Observador de Rede|Nome do grupo de recursos onde estão localizados os Observadores de Rede|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de Política|Definição de auditoria necessária para servidores SQL||

## <a name="next-steps"></a>Passos seguintes

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).