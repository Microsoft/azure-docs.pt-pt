---
title: Configurar definições personalizadas
description: Configurar configurações que se aplicam a todo o ambiente do Serviço de Aplicações Azure. Aprenda a fazê-lo com modelos do Azure Resource Manager.
author: ccompy
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.topic: tutorial
ms.date: 01/29/2021
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 5c1e81d02aa35a40a296f04e456be09eeed10331
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99226393"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>Configurações de configuração personalizadas para Ambientes de Serviço de Aplicações
## <a name="overview"></a>Descrição geral
Como os Ambientes de Serviço de Aplicações (ASEs) estão isolados a um único cliente, existem determinadas definições de configuração que podem ser aplicadas exclusivamente a Ambientes de Serviço de Aplicações. Este artigo documenta as várias personalizações específicas que estão disponíveis para Ambientes de Serviço de Aplicações.

Se não tiver um Ambiente de Serviço de Aplicações, consulte [como criar um Ambiente de Serviço de Aplicações.](app-service-web-how-to-create-an-app-service-environment.md)

Pode armazenar personalizações do App Service Environment utilizando uma matriz no novo **clusterSettings atribuem.** Este atributo encontra-se no dicionário "Propriedades" da entidade *gestora* de recursos Azure.

O seguinte corte de modelo abreviado do Gestor de Recursos mostra o **atributo clusterSettings:**

```json
"resources": [
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/hostingEnvironments",
    "name": ...,
    "location": ...,
    "properties": {
        "clusterSettings": [
            {
                "name": "nameOfCustomSetting",
                "value": "valueOfCustomSetting"
            }
        ],
        "workerPools": [ ...],
        etc...
    }
}
```

O **atributo clusterSettings** pode ser incluído num modelo de Gestor de Recursos para atualizar o Ambiente de Serviço de Aplicações.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Use o Azure Resource Explorer para atualizar um Ambiente de Serviço de Aplicações
Em alternativa, pode atualizar o Ambiente de Serviço de Aplicações utilizando [o Azure Resource Explorer](https://resources.azure.com).  

1. No Resource Explorer, aceda ao nó para o Ambiente de Serviço de **Aplicações (fornecedores** de recursos de  >  **subscrições Grupos de**  >    >  **grupos Microsoft.Web**  >  **hospedando Ambientes).** Em seguida, clique no Ambiente de Serviço de Aplicações específico que pretende atualizar.
2. No painel direito, clique em **Ler/Escrever** na barra superior para permitir a edição interativa no Explorador de Recursos.  
3. Clique no botão **de Edição** azul para tornar o modelo de Gestor de Recursos editável.
4. Percorra para o fundo do painel direito. O **atributo clusterSettings** encontra-se na parte inferior, onde pode introduzir ou atualizar o seu valor.
5. Digite (ou copie e cole) a matriz de valores de configuração que deseja no **atributo clusterSettings.**  
6. Clique no botão **PUT** verde que está localizado na parte superior do painel direito para comprometer a alteração para o Ambiente de Serviço de Aplicações.

Seja como for que submeta a alteração, leva cerca de 30 minutos multiplicados pelo número de extremidades frontais no Ambiente de Serviço de Aplicações para que a alteração produza efeitos.
Por exemplo, se um Ambiente de Serviço de Aplicações tiver quatro extremidades dianteiras, levará cerca de duas horas para a atualização de configuração terminar. Enquanto a alteração de configuração está a ser lançada, nenhuma outra operação de escala ou alteração de configuração pode ocorrer no Ambiente de Serviço de Aplicações.

## <a name="enable-internal-encryption"></a>Ativar encriptação interna

O App Service Environment funciona como um sistema de caixa preta onde não é possível ver os componentes internos ou a comunicação dentro do sistema. Para ativar uma produção mais elevada, a encriptação não é ativada por padrão entre componentes internos. O sistema é seguro uma vez que o tráfego é inacessível a ser monitorizado ou acedido. Se tiver um requisito de conformidade que exija encriptação completa do caminho dos dados de ponta a ponta, existe uma forma de permitir a encriptação do caminho completo dos dados com um clusterSetting.  

```json
"clusterSettings": [
    {
        "name": "InternalEncryption",
        "value": "true"
    }
],
```
Definir InternalEncryption para encriptar o tráfego interno da rede no seu ASE entre as extremidades dianteiras e os trabalhadores, encripta o ficheiro de página e também encripta os discos de trabalho. Após o cluster de encriptação internaSeçãoSetting está ativado, pode haver um impacto no desempenho do seu sistema. Quando fizer a alteração para ativar o InternalEncrypation, o seu ASE ficará instável até que a alteração seja totalmente propagada. A propagação completa da alteração pode demorar algumas horas a ser concluída, dependendo do número de ocorrências que tem no seu ASE. Recomendamos vivamente que não ative o InternalEnsencryption num ASE durante a sua utilização. Se precisar de ativar o InternalEncryption num ASE ativamente utilizado, recomendamos vivamente que desvie o tráfego para um ambiente de reserva até que a operação esteja concluída. 


## <a name="disable-tls-10-and-tls-11"></a>Desativar o TLS 1.0 e o TLS 1.1

Se pretender gerir as definições de TLS numa aplicação por app, então pode utilizar as orientações fornecidas com a documentação [de definições de Imposição.](../configure-ssl-bindings.md#enforce-tls-versions) 

Se pretender desativar todos os tráfegos TLS 1.0 e TLS 1.1 de entrada para todas as aplicações numa ASE, pode definir a seguinte entrada **de clusterSettings:**

```json
"clusterSettings": [
    {
        "name": "DisableTls1.0",
        "value": "1"
    }
],
```

O nome da definição diz 1.0 mas, quando configurado, desativa tanto o TLS 1.0 como o TLS 1.1.

## <a name="change-tls-cipher-suite-order"></a>Alterar pedido de suíte de cifra TLS
O ASE suporta a alteração da suíte de cifra do padrão. O conjunto padrão de cifras é o mesmo conjunto que é usado no serviço multi-inquilino. A alteração das suítes de cifra afeta toda uma implementação do Serviço de Aplicações, tornando isso possível apenas no ASE de um único inquilino. São necessárias duas suítes cifradas para um ASE; TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 e TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256. Se desejar operar o seu ASE com o conjunto mais forte e mínimo de suítes cifras, use apenas as duas cifras necessárias. Para configurar o seu ASE para utilizar apenas as cifras que necessita, modifique os **clustersSettings** como mostrado abaixo. 

```json
"clusterSettings": [
    {
        "name": "FrontEndSSLCipherSuiteOrder",
        "value": "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256"
    }
],
```

> [!WARNING]
> Se forem definidos valores incorretos para o conjunto de cifras que o SChannel não consegue compreender, toda a comunicação TLS ao seu servidor poderá deixar de funcionar. Neste caso, terá de remover a entrada *FrontEndSSLCipherSuiteOrder* dos **clusterSettings** e submeter o modelo atualizado do Gestor de Recursos para voltar às definições padrão da suite de cifra.  Por favor, utilize esta funcionalidade com cuidado.

## <a name="get-started"></a>Introdução
O site do modelo Azure Quickstart Resource Manager inclui um modelo com a definição base para [criar um Ambiente de Serviço de Aplicação.](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/)

<!-- LINKS -->

<!-- IMAGES -->
