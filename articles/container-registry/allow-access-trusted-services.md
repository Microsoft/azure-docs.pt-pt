---
title: Aceder a registo restrito de rede utilizando o serviço fidedigno Azure
description: Permitir que uma instância de serviço fidedigna Azure aceda de forma segura a um registo restrito de contentores em rede para puxar ou empurrar imagens
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 2e6b6ee3736f98f53ebb0aa43d707d42ba4cc058
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527903"
---
# <a name="allow-trusted-services-to-securely-access-a-network-restricted-container-registry-preview"></a>Permitir serviços fidedignos para aceder de forma segura a um registo restrito de contentores em rede (pré-visualização)

O Registo de Contentores Azure pode permitir que os serviços de Azure fidedignos possam aceder a um registo configurado com regras de acesso à rede. Quando os serviços fidedignos são permitidos, uma instância de serviço fidedigna pode contornar de forma segura as regras de rede do registo e executar operações como puxar ou empurrar imagens. A identidade gerida da instância de serviço é utilizada para acesso e deve ser atribuída uma função Azure e autenticar-se com o registo.

Utilize o Azure Cloud Shell ou uma instalação local do CLI Azure para executar os exemplos de comando neste artigo. Se quiser usá-lo localmente, a versão 2.18 ou mais tarde é necessária. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

Permitir o acesso ao registo por serviços fidedignos da Azure é uma funcionalidade **de pré-visualização.**

## <a name="limitations"></a>Limitações

* Deve utilizar uma identidade gerida atribuída pelo sistema, ativada num [serviço de confiança,](#trusted-services) para aceder a um registo restrito de contentores em rede. As identidades geridas atribuídas pelo utilizador não são suportadas atualmente.
* Permitir serviços fidedignos não se aplica a um registo de contentores configurado com um [ponto final de serviço](container-registry-vnet.md). A funcionalidade apenas afeta registos restritos a um [ponto final privado](container-registry-private-link.md) ou que tenham [regras públicas de acesso a IP aplicadas.](container-registry-access-selected-networks.md) 

## <a name="about-trusted-services"></a>Sobre serviços de confiança

O Registo de Contentores Azure tem um modelo de segurança em camadas, suportando múltiplas configurações de rede que restringem o acesso a um registo, incluindo:

* [Ponto final privado com Ligação Privada Azure](container-registry-private-link.md). Quando configurado, o ponto final privado de um registo é acessível apenas a recursos dentro da rede virtual, utilizando endereços IP privados.  
* [Regras de firewall de registo](container-registry-access-selected-networks.md), que permitem o acesso ao ponto final público do registo apenas a partir de endereços IP públicos específicos ou intervalos de endereços. Também pode configurar a firewall para bloquear todo o acesso ao ponto final público quando utilizar pontos finais privados.

Quando implementado numa rede virtual ou configurado com regras de firewall, um registo nega o acesso por padrão a utilizadores ou serviços de fora dessas fontes. 

Vários serviços multi-inquilinos Azure operam a partir de redes que não podem ser incluídas nestas configurações da rede de registos, impedindo-os de puxar ou empurrar imagens para o registo. Ao designar certas instâncias de serviço como "fidedignas", um proprietário de registo pode permitir selecionar recursos Azure para contornar de forma segura as definições de rede do registo para puxar ou empurrar imagens. 

### <a name="trusted-services"></a>Serviços de confiança

Os casos dos seguintes serviços podem aceder a um registo restrito de contentores em rede se a definição de **serviços fidedignos** do registo estiver ativada (o padrão). Mais serviços serão adicionados ao longo do tempo.

|Serviço de confiança  |Cenários de utilização apoiados  |
|---------|---------|
|Tarefas ACR     | [Aceda a um registo diferente de uma tarefa ACR](container-registry-tasks-cross-registry-authentication.md)       |
|Machine Learning | [Implementar](../machine-learning/how-to-deploy-custom-docker-image.md) ou [treinar](../machine-learning/how-to-train-with-custom-image.md) um modelo num espaço de trabalho machine learning usando uma imagem personalizada do recipiente Docker |
|Registo de Contentores do Azure | [Importar imagens de outro registo de contentores Azure](container-registry-import-images.md#import-from-an-azure-container-registry-in-the-same-ad-tenant) | 

> [!NOTE]
> Com a cura, permitir a definição de serviços fidedignos não permite que casos de outros serviços geridos da Azure, incluindo o Serviço de Aplicações, instâncias de contentores Azure e Azure Security Center, acedam a um registo restrito de contentores em rede.

## <a name="allow-trusted-services---cli"></a>Permitir serviços de confiança - CLI

Por predefinição, a definição de serviços fidedignos permite estar ativada num novo registo de contentores Azure. Desative ou ative a definição executando o comando [de atualização az acr.](/cli/azure/acr#az-acr-update)

Para desativar:

```azurecli
az acr update --name myregistry --allow-trusted-services false
```

Para ativar a definição num registo existente ou num registo onde já esteja desativado:

```azurecli
az acr update --name myregistry --allow-trusted-services true
```

## <a name="allow-trusted-services---portal"></a>Permitir serviços fidedignos - portal

Por predefinição, a definição de serviços fidedignos permite estar ativada num novo registo de contentores Azure. 

Para desativar ou reativar a definição no portal:

1. No portal, navegue para o registo do seu contentor.
1. Em **Definições**, selecione **Networking**. 
1. No **Permitir o acesso à rede pública,** selecione redes **selecionadas** ou **desativadas.**
1. Faça um dos seguintes:
    * Para desativar o acesso por serviços fidedignos, sob **exceção de Firewall**, **desmarque,.** 
    * Para permitir serviços fidedignos, sob **exceção firewall,** verifique **Se os serviços fidedignos da Microsoft acedem a este registo de contentores.**
1. Selecione **Guardar**.

## <a name="trusted-services-workflow"></a>Fluxo de trabalho de serviços fidedignos

Aqui está um fluxo de trabalho típico para permitir que uma instância de um serviço de confiança aceda a um registo restrito de contentores em rede.

1. Ativar uma identidade gerida atribuída pelo sistema [para os recursos Azure,](../active-directory/managed-identities-azure-resources/overview.md) num caso de um dos [serviços fidedignos](#trusted-services) do Registo de Contentores Azure.
1. Atribua a identidade a um [papel de Azure](container-registry-roles.md) no seu registo. Por exemplo, atribua o papel ACRPull para puxar imagens de contentores.
1. No registo restrito à rede, configurar a definição para permitir o acesso por serviços fidedignos.
1. Utilize as credenciais da identidade para autenticar com o registo restrito à rede. 
1. Retire as imagens do registo ou execute outras operações permitidas pelo papel.

### <a name="example-acr-tasks"></a>Exemplo: Tarefas ACR

O exemplo a seguir demonstra a utilização de Tarefas ACR como um serviço de confiança. Consulte [a autenticação de registo cruzado numa tarefa ACR utilizando uma identidade gerida pelo Azure](container-registry-tasks-cross-registry-authentication.md) para obter detalhes da tarefa.

1. Crie ou atualize um registo de contentores Azure e [empurre uma imagem base](container-registry-tasks-cross-registry-authentication.md#prepare-base-registry) de amostra para o registo. Este registo é o *registo base* do cenário.
1. Num segundo registo de contentores Azure, [defina](container-registry-tasks-cross-registry-authentication.md#define-task-steps-in-yaml-file) e [crie](container-registry-tasks-cross-registry-authentication.md#option-2-create-task-with-system-assigned-identity) uma tarefa ACR para retirar uma imagem do registo base. Ativar uma identidade gerida atribuída pelo sistema ao criar a tarefa.
1. Atribua à identidade de tarefa [uma função Azure para aceder ao registo base](container-registry-tasks-authentication-managed-identity.md#3-grant-the-identity-permissions-to-access-other-azure-resources). Por exemplo, atribua o papel AcrPull, que tem permissões para puxar imagens.
1. [Adicione credenciais de identidade geridas](container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task) à tarefa.
1. Para confirmar que a tarefa contorna as restrições de rede, [desative](container-registry-access-selected-networks.md#disable-public-network-access) o acesso do público no registo base.
1. Executar a tarefa. Se o registo e a tarefa base estiverem configurados corretamente, a tarefa é executada com sucesso, porque o registo base permite o acesso.

Para testar o acesso incapacitante por serviços fidedignos:

1. No registo base, desative a definição para permitir o acesso por serviços fidedignos.
1. Executar a tarefa de novo. Neste caso, a execução de tarefas falha, porque o registo base já não permite o acesso através da tarefa.

## <a name="next-steps"></a>Passos seguintes

* Para restringir o acesso a um registo utilizando um ponto final privado numa rede virtual, consulte o [Link Privado Configure Azure para um registo de contentores Azure](container-registry-private-link.md).
* Para configurar as regras de firewall de registo, consulte as [regras de rede IP públicas configure](container-registry-access-selected-networks.md).
