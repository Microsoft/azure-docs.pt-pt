---
title: Tipos de test drives, mercado comercial da Microsoft
description: Tipos de test drives no mercado comercial
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/19/2020
ms.author: trkeya
author: trkeya
ms.openlocfilehash: 0b445f9d4fdda0b1fac9dcdb4344533cfd7d37a9
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491244"
---
# <a name="azure-resource-manager-test-drive"></a>Unidade de teste do Gestor de Recursos Azure

Utilize este tipo se tiver uma oferta no Azure Marketplace ou appSource mas quiser construir um test drive apenas com recursos Azure. Um modelo Azure Resource Manager (ARM) é um recipiente codificado de recursos Azure que projeta para melhor representar a sua solução. O test drive pega no modelo ARM fornecido e implementa todos os recursos necessários para um grupo de recursos. Esta é a única opção de test drive para máquina virtual ou aplicação Azure.

Se não está familiarizado com o que é um modelo ARM, leia [O que é Azure Resource Manager?](../azure-resource-manager/management/overview.md) [Understand the structure and syntax of ARM templates](../azure-resource-manager/templates/template-syntax.md)

Para obter informações sobre um test drive de aplicações **hospedados** ou **lógicos,** veja [o que é um test drive?](what-is-test-drive.md)

## <a name="technical-configuration"></a>Configuração técnica

Um modelo de implementação contém todos os recursos Azure que compõem a sua solução. Os produtos que se adequam a este cenário utilizam apenas recursos Azure. Desaguise as seguintes propriedades no Partner Center:

- **Regiões (necessárias)** – Atualmente existem 26 regiões apoiadas pelo Azure onde o seu test drive pode ser disponibilizado. Normalmente, você vai querer disponibilizar o seu test drive nas regiões onde você antecipa o maior número de clientes, para que eles possam selecionar a região mais próxima para o melhor desempenho. Terá de se certificar de que a sua subscrição pode implementar todos os recursos necessários em cada uma das regiões que está a selecionar.

- **Instâncias** – Selecione o tipo (quente ou frio) e o número de instâncias disponíveis, que serão multiplicadas pelo número de regiões onde a sua oferta está disponível.

  - **Quente** – Este tipo de ocorrência é implantado e aguarda acesso por região selecionada. Os clientes podem aceder instantaneamente a casos *quentes* de um test drive, em vez de terem de esperar por uma implementação. A troca é que estes casos estão sempre a funcionar na sua subscrição do Azure, por isso incorrerão num custo de uptime maior. É altamente recomendado ter pelo menos um caso *Hot,* uma vez que a maioria dos clientes não quer esperar por implementações completas, resultando numa entrega no uso do cliente se não houver nenhuma instância *Hot* disponível.

  - **Frio** – Este tipo de ocorrência representa o número total de casos que podem ser implantados por região. As instâncias frias requerem que todo o modelo do Gestor de Recursos de Test Drive implemente quando um cliente solicita o test drive, por isso as instâncias *de frio* são muito mais lentas a carregar do que as instâncias *hot.* A troca é que só tem de pagar a duração do test drive, *nem* sempre está a funcionar na sua subscrição Azure como numa instância *Hot.*

- **Test drive Modelo de Gestor de Recursos Azure** – Carreque o .zip contendo o seu modelo de Gestor de Recursos Azure. Saiba mais sobre a criação de um modelo de Gestor de Recursos Azure no artigo de arranque rápido [Criar e implementar modelos de Gestor de Recursos Azure utilizando o portal Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

    > [!note]
    > Para publicar com sucesso, é importante validar a formatação do modelo ARM. Duas formas de o fazer são (1) utilizando uma [ferramenta API online](https://docs.microsoft.com/rest/api/resources/deployments/validate) ou (2) com uma [implementação de teste](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal).

- **Duração do test drive** (obrigatório) – Introduza o número de horas em que o test drive permanecerá ativo. A unidade de teste termina automaticamente após o fim deste período de tempo. Utilize apenas números inteiros (por exemplo, "2" horas é válido, "1,5" não é).

## <a name="write-the-test-drive-template"></a>Escreva o modelo de unidade de teste

Depois de ter desenhado o pacote de recursos pretendido, escreva e construa o modelo ARM de test drive. Como a unidade de teste executa implementações num modo totalmente automatizado, os modelos de test drive têm algumas restrições:

### <a name="parameters"></a>Parâmetros

A maioria dos modelos tem um conjunto de parâmetros que definem nomes de recursos, tamanhos de recursos (tais como tipos de contas de armazenamento ou tamanhos de máquinas virtuais), nomes de utilizador e palavras-passe, nomes DNS, e assim por diante. Quando implementa soluções utilizando o portal Azure, pode preencher manualmente todos estes parâmetros, escolher nomes DNS disponíveis ou nomes de contas de armazenamento, e assim por diante.

![Lista de parâmetros num Gestor de Recursos Azure](media/test-drive/resource-manager-parameters.png)

No entanto, o test drive funciona automaticamente, sem interação humana, pelo que suporta apenas um conjunto limitado de categorias de parâmetros. Se um parâmetro no modelo ARM de test drive não cair numa das categorias suportadas, deve substituir este parâmetro por um valor variável ou constante.

Pode utilizar qualquer nome válido para os seus parâmetros; a unidade de teste reconhece a categoria de parâmetros utilizando um valor do tipo metadados. Especifique o tipo de metadados para cada parâmetro do modelo, caso contrário o seu modelo não passará a validação:

```JSON
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username"
    }
  },
  ...
}
```

> [!NOTE]
> Todos os parâmetros são opcionais, por isso, se não quiseres usar nenhum, não tens de o fazer.

### <a name="accepted-parameter-metadata-types"></a>Tipos de metadados de parâmetros aceites

| Tipo de metadados   | Tipo de parâmetro  | Description     | Valor da amostra    |
|---|---|---|---|
| **baseuri**     | string          | Base URI do seu pacote de implantação| `https:\//\<\..\>.blob.core.windows.net/\<\..\>` |
| **nome de utilizador**    | string          | Novo nome de utilizador aleatório.| administrador68876      |
| **palavra-passe**    | cadeia segura    | Nova senha aleatória | Lp!ACS \^ 2kh     |
| **id sessão**   | string          | ID de sessão de test drive único (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="baseuri"></a>baseuri

A unidade de teste inindo este parâmetro com um **Base Uri** do seu pacote de implantação para que possa utilizar este parâmetro para construir um Uri de qualquer ficheiro incluído no seu pacote.

```JSON
"parameters": {
  ...
  "baseuri": {
    "type": "string",
    "metadata": {
      "type": "baseuri",
      "description": "Base Uri of the deployment package."
    }
  },
  ...
}
```

Utilize este parâmetro dentro do seu modelo para construir um Uri de qualquer ficheiro a partir do seu pacote de implementação de test drive. O exemplo a seguir mostra como construir um Uri do modelo ligado:

```JSON
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>nome de utilizador

A unidade de teste ini (test drive) inindo este parâmetro com um novo nome de utilizador aleatório:

```JSON
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username",
      "description": "Solution admin name."
    }
  },
  ...
}
```

Valor da amostra: `admin68876`

Pode utilizar nomes de utilizador aleatórios ou constantes para a sua solução.

#### <a name="password"></a>palavra-passe

O test drive iniciona este parâmetro com uma nova senha aleatória:

```JSON
"parameters": {
  ...
  "password": {
    "type": "securestring",
    "metadata": {
      "type": "password",
      "description": "Solution admin password."
    }
  },
  ...
}
```

Valor da amostra:  `Lp!ACS^2kh`

Pode utilizar senhas aleatórias ou constantes para a sua solução.

#### <a name="session-id"></a>ID da sessão

O test drive iniciona este parâmetro com um ID de sessão de unidade de teste único que representa o teste:

```JSON
"parameters": {
  ...
  "sessionid": {
    "type": "string",
    "metadata": {
      "type": "sessionid",
      "description": "Unique test drive session id."
    }
  },
  ...
}
```

Valor da amostra: `b8c8693e-5673-449c-badd-257a405a6dee`

Pode utilizar este parâmetro para identificar exclusivamente a sessão de test drive, se for necessário.

### <a name="unique-names"></a>Nomes Únicos

Alguns recursos Azure, como contas de armazenamento ou nomes DNS, requerem nomes globalmente únicos. Isto significa que cada vez que o test drive implementa o modelo ARM, cria um novo grupo de recursos com um nome único para todos os seus recursos. Portanto, deve utilizar a função [de base única](../azure-resource-manager/templates/template-functions.md) concatenada com os seus nomes variáveis em IDs do grupo de recursos para gerar valores únicos aleatórios:

```JSON
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

Certifique-se de que concatena o seu parâmetro/cadeias variáveis `contosovm` com uma saída de corda única ( ), porque isso garante a `resourceGroup().id` singularidade e fiabilidade de cada variável.

Por exemplo, a maioria dos nomes de recursos não pode começar com um dígito, mas a função de corda única pode devolver uma corda, que começa com um dígito. Por isso, se utilizares uma saída de corda única e crua, as tuas implementações falharão.

Pode encontrar informações adicionais sobre regras e restrições de nomeação de recursos [neste artigo.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)

### <a name="deployment-location"></a>Localização de implantação

Você pode torná-lo test drive disponível em diferentes regiões de Azure. A ideia é permitir que um utilizador escolha a região mais próxima, para proporcionar a experiência do utilizador da besta.

Quando o test drive cria uma instância do Laboratório, cria sempre um grupo de recursos na região escolhido por um utilizador e executa o seu modelo de implementação neste contexto de grupo. Assim, o seu modelo deve escolher a localização de implementação do grupo de recursos:

```JSON
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

E, em seguida, use este local para cada recurso para uma instância específica do Laboratório:

```JSON
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/publicIPAddresses",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/virtualNetworks",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/networkInterfaces",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "location": "[variables('location')]",
    ...
  }
]
```

Certifique-se de que a sua subscrição é permitida para implementar todos os recursos que deseja em cada uma das regiões que seleciona. Também certifique-se de que as suas imagens de máquinas virtuais estão disponíveis em todas as regiões que irá ativar, caso contrário o seu modelo de implantação não funcionará para algumas regiões.

### <a name="outputs"></a>Saídas

Normalmente com os modelos do Gestor de Recursos pode implementar sem produzir qualquer saída. Isto porque conhece todos os valores que utiliza para preencher parâmetros do modelo e pode sempre inspecionar manualmente propriedades de qualquer recurso.

No entanto, para os modelos de gestor de recursos de test drive, é importante voltar a testar toda a informação, que é necessária para ter acesso ao laboratório (URIs do site, nomes de anfitriões de máquinas virtuais, nomes de utilizador e palavras-passe). Certifique-se de que todos os seus nomes de saída são legíveis porque estas variáveis são apresentadas ao cliente.

Não existem restrições relacionadas com as saídas do modelo. A unidade de teste converte todos os valores de saída em cordas, por isso, se enviar um objeto para a saída, um utilizador verá a cadeia JSON.

Exemplo:

```JSON
"outputs": {
  "Host Name": {
    "type": "string",
    "value": "[reference(variables('pubIpId')).dnsSettings.fqdn]"
  },
  "User Name": {
    "type": "string",
    "value": "[parameters('adminName')]"
  },
  "Password": {
    "type": "string",
    "value": "[parameters('adminPassword')]"
  }
}
```

### <a name="subscription-limits"></a>Limites de subscrição

Não se esqueça dos limites de subscrição e serviço. Por exemplo, se pretender implantar até dez máquinas virtuais de 4 núcleos, tem de garantir que a subscrição que utiliza para o seu laboratório lhe permite utilizar 40 núcleos. Para obter mais informações sobre os limites de subscrição e serviço da [Azure, consulte os limites de subscrição e serviços da Azure, quotas e restrições.](../azure-resource-manager/management/azure-subscription-service-limits.md) Como várias unidades de teste podem ser tomadas ao mesmo tempo, verifique se a sua subscrição pode lidar com o número de núcleos multiplicados pelo número total de test drives simultâneos que podem ser tomadas.

### <a name="what-to-upload"></a>O que carregar

O modelo ARM de test drive é carregado como um ficheiro zip, que pode incluir vários artefactos de implantação, mas deve ter um ficheiro nomeado `main-template.json` . Este é o modelo de implantação ARM que o test drive usa para instantaneaizar um laboratório. Se tiver recursos adicionais para além deste ficheiro, pode remí-los como recursos externos dentro do modelo ou incluí-los no ficheiro zip.

Durante a certificação de publicação, a unidade de teste abre o seu pacote de implantação e coloca o seu conteúdo num recipiente interno de test drive blob. A estrutura do contentor reflete a estrutura do seu pacote de implantação:

| package.zip                       | Recipiente blob de test drive         |
|---|---|
| `main-template.json`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/main-template.json`  |
| `templates/solution.json`           | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/templates/solution.json` |
| `scripts/warmup.ps1`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/scripts/warmup.ps1`  |
|||

Chamamos um Uri deste contentor blob Base Uri. Porque cada revisão do seu laboratório tem o seu próprio recipiente blob, cada revisão do seu laboratório tem a sua própria Base Uri. O test drive pode passar uma Base Uri do seu pacote de implementação desapertado no seu modelo através de parâmetros de modelo.

### <a name="transform-template-examples-for-test-drive"></a>Transforme exemplos de modelo para test drive

O processo de transformar uma arquitetura de recursos num modelo de gestor de recursos de test drive pode ser assustador. Para obter ajuda adicional, consulte estes exemplos de como melhor transformar os modelos de implementação atuais em [O que é um test drive?](what-is-test-drive.md#transforming-examples)

## <a name="test-drive-deployment-subscription-details"></a>Detalhes da subscrição da subscrição da unidade de teste

A secção final a completar é poder implementar automaticamente as unidades de teste ligando a subscrição do Azure e o Azure Ative Directory (AD).

![Detalhes da subscrição da subscrição da unidade de teste](media/test-drive/deployment-subscription-details.png)

1. Obtenha um **ID de assinatura Azure**. Isto permite o acesso aos serviços Azure e ao portal Azure. A subscrição é onde o uso de recursos é reportado e os serviços são faturados. Se ainda não tiver uma subscrição Azure separada apenas para test drives, faça uma. Pode encontrar IDs de subscrição do Azure (tais `1a83645ac-1234-5ab6-6789-1h234g764ghty1` como) insinando-se no portal Azure e selecionando **Subscrições** a partir do menu de navegação à esquerda.

   ![Subscrições do Azure](media/test-drive/azure-subscriptions.png)

2. Obtenha uma identificação do inquilino da **AD Azure.** Se já tem um ID de inquilino disponível, pode encontrá-lo no ID do Diretório De Diretório Ativo **Azure:**  >  **Properties**  >  **Directory ID**

   ![Propriedades do Diretório Ativo Azure](media/test-drive/azure-active-directory-properties.png)

   Se não tiver uma identificação de inquilino, crie uma nova no Diretório Ativo Azure. Para obter ajuda na constituição de um inquilino, consulte [Quickstart: Crie um inquilino.](../active-directory/develop/quickstart-create-new-tenant.md)

3. **ID da App AD Azure** – Criar e registar uma nova aplicação. Usaremos esta aplicação para realizar operações na sua instância de test drive.

   1. Navegue para o diretório recém-criado ou já existente e selecione O Diretório Ativo Azure no painel de filtros.
   2. Pesquisar **registos de Aplicativos** e selecionar **Adicionar.**
   3. Forneça um nome de inscrição.
   4. Selecione o **tipo** de **aplicação Web / API**.
   5. Fornecer qualquer valor no URL de inscrição, este campo não é usado.
   6. Selecione **Criar**.
   7. Depois de a aplicação ter sido criada, selecione **Properties**  >  **set the application como multi-inquilino** e, em seguida, **Guardar**.

4. Selecione **Guardar**.

5. Copie o ID da aplicação para esta aplicação registada e cole-a no campo de test drive.

   ![Detalhe de ID de aplicação AZure](media/test-drive/azure-ad-application-id-detail.png)

6. Uma vez que estamos a usar a aplicação para implementar na subscrição, precisamos adicionar a aplicação como contribuinte na subscrição:

   1. Selecione o tipo de **Subscrição** que está a utilizar para a unidade de teste.
   1. Selecione **Controlo de acesso (IAM)** .
   1. Selecione o **separador atribuições de funções** e, em seguida, **Adicione a atribuição de funções**.

      ![Adicione um novo diretor de Controlo de Acesso](media/test-drive/access-control-principal.jpg)

   1. Desa **parter o** acesso e **atribuir o acesso ao** que for mostrado. No campo **Select,** insira o nome da aplicação Azure AD. Selecione a aplicação à qual pretende atribuir a **função Contribuinte.**

      ![Adicione as permissões](media/test-drive/access-control-permissions.jpg)

   1. Selecione **Guardar**.

7. Gere uma chave de autenticação **AD AD Azure.** Em **Teclas** , adicione uma **descrição da chave** , desembaraça a duração de Nunca **expira** (uma tecla expirada quebrará o seu test drive na produção), em seguida, selecione **Save**. Copie e cole este valor no campo de teste necessário.

![Mostra as Chaves para a aplicação AD Azure](media/test-drive/azure-ad-app-keys.png)

## <a name="republish"></a>Voltar a publicar

Agora que todos os seus campos de test drive estão completos, **resubscreva** a sua oferta. Uma vez que o seu test drive tenha passado a certificação, teste a experiência do cliente na pré-visualização da sua oferta:

1. Inicie um test drive na UI.
1. Abra a sua subscrição Azure dentro do portal Azure.
1. Verifique se o seu test drive está a ser acionado corretamente.

   ![Portal do Azure](media/test-drive/azure-portal.png)

Não elimine quaisquer instâncias de test drive previstas para os seus clientes; o serviço de test drive limpará automaticamente estes Grupos de Recursos depois de um cliente terminar com ele.

Uma vez confortável com a sua oferta de pré-visualização, é hora de ir ao **vivo!** Há um processo de revisão final para verificar novamente toda a experiência de ponta a ponta. Se rejeitarmos a oferta, enviaremos um e-mail ao contacto de engenharia para a sua oferta explicando o que precisa de ser corrigido.

## <a name="next-steps"></a>Passos seguintes

- Se seguia as instruções para criar a sua oferta no Partner Center, utilize a seta traseira para voltar a esse tópico.
- Saiba mais sobre outros tipos de test drives no [What is a test drive?](what-is-test-drive.md)