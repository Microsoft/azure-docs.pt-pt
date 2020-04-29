---
title: Configurar definições personalizadas
description: Configure as definições aplicáveis a todo o ambiente do Serviço de Aplicações Azure. Aprenda a fazê-lo com modelos de Gestor de Recursos Azure.
author: stefsch
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.topic: tutorial
ms.date: 12/19/2019
ms.author: stefsch
ms.custom: mvc, seodec18
ms.openlocfilehash: 25393007a3cc878737ea5927cb65bcf7ef945313
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80057573"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>Configurações de configuração personalizadas para ambientes de serviço de aplicações
## <a name="overview"></a>Descrição geral
Uma vez que os Ambientes de Serviço de Aplicações (ASE) estão isolados a um único cliente, existem certas configurações de configuração que podem ser aplicadas exclusivamente aos Ambientes de Serviço de Aplicações. Este artigo documenta as várias personalizações específicas que estão disponíveis para ambientes de serviço de aplicações.

Se não tiver um Ambiente de Serviço de Aplicações, consulte como criar um ambiente de [serviço de aplicações.](app-service-web-how-to-create-an-app-service-environment.md)

Pode armazenar personalizações de ambiente de serviço de aplicação utilizando uma matriz no atributo de novas **clusterSettings.** Este atributo encontra-se no dicionário "Propriedades" da entidade gestora de recursos do *Ambientes* Azure.

O seguinte modelo abreviado do Gestor de Recursos mostra o atributo **clusterSettings:**

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

O atributo **clusterSettings** pode ser incluído num modelo de Gestor de Recursos para atualizar o Ambiente do Serviço de Aplicações.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Use o Azure Resource Explorer para atualizar um Ambiente de Serviço de Aplicações
Em alternativa, pode atualizar o Ambiente do Serviço de Aplicações utilizando o [Azure Resource Explorer](https://resources.azure.com).  

1. No Resource Explorer, vá ao nó para o Ambiente do Serviço > de Aplicações >  > **(recursos de****subscriçãoFornecedores** > **providers****Microsoft.Web****hostingEnvironments**). Em seguida, clique no ambiente específico do Serviço de Aplicações que pretende atualizar.
2. No painel certo, clique em **Ler/Escrever** na barra superior para permitir a edição interativa no Resource Explorer.  
3. Clique no botão **de Edição** azul para tornar o modelo de Gestor de Recursos editável.
4. Percorra para o fundo do painel direito. O **atributo clusterSettings** está na parte inferior, onde pode entrar ou atualizar o seu valor.
5. Digite (ou copie e cole) a matriz de valores de configuração que deseja no atributo **clusterSettings.**  
6. Clique no botão **PUT** verde que está localizado na parte superior do painel direito para comprometer a alteração para o Ambiente de Serviço de Aplicações.

Seja como for que submeta a alteração, demora cerca de 30 minutos multiplicado pelo número de extremidades dianteiras no Ambiente de Serviço de Aplicações para que a alteração entre em vigor.
Por exemplo, se um Ambiente de Serviço de Aplicações tiver quatro extremidades dianteiras, levará cerca de duas horas para que a atualização de configuração termine. Enquanto a mudança de configuração está a ser lançada, nenhuma outra operação de escala ou operações de alteração de configuração pode ocorrer no Ambiente de Serviço de Aplicações.

## <a name="enable-internal-encryption"></a>Ativar encriptação interna

O App Service Environment funciona como um sistema de caixa preta onde não é possível ver os componentes internos ou a comunicação dentro do sistema. Para permitir uma maior entrada, a encriptação não é ativada por padrão entre componentes internos. O sistema é seguro, uma vez que o tráfego é completamente inacessível a ser monitorizado ou acedido. Se tiver um requisito de conformidade, embora isso exija uma encriptação completa do caminho dos dados de ponta a ponta, existe uma maneira de o ativar com um clusterSetting.  

        "clusterSettings": [
            {
                "name": "InternalEncryption",
                "value": "1"
            }
        ],
 
Depois de ativado o clusterSystem InternalEncryption, pode haver um impacto no desempenho do seu sistema. Quando fizer a alteração para permitir a InternalEncryption, a sua ASE estará num estado instável até que a mudança seja totalmente propagada. A propagação completa da alteração pode demorar algumas horas a ser completada, dependendo do número de casos que tem na Sua ASE. Recomendamos vivamente que não o ative numa ASE enquanto estiver a ser utilizado. Se precisar de ativar isto numa ASE ativamente utilizada, recomendamos vivamente que desvie o tráfego para um ambiente de reserva até que a operação esteja concluída. 

## <a name="disable-tls-10-and-tls-11"></a>Desativar TLS 1.0 e TLS 1.1

Se pretender gerir as definições de TLS numa aplicação por app, então pode utilizar a orientação fornecida com a documentação de [definições 'Enforce TLS'.](../configure-ssl-bindings.md#enforce-tls-versions) 

Se pretender desativar todo o tráfego TLS 1.0 e TLS 1.1 para todas as aplicações numa ASE, pode definir a seguinte entrada **clusterSettings:**

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

O nome da definição diz 1.0 mas quando configurado, desativa tanto o TLS 1.0 como o TLS 1.1.

## <a name="change-tls-cipher-suite-order"></a>Alterar encomenda suíte de cifra TLS
Outra questão dos clientes é se podem modificar a lista de cifras negociadas pelo seu servidor e isso pode ser alcançado modificando as **ClusterSettings** como mostrado abaixo. A lista de suítes cifra disponíveis pode ser recuperada a partir deste artigo da [MSDN](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx).

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [!WARNING]
> Se forem definidos valores incorretos para o suite cifra que o SChannel não consegue compreender, toda a comunicação TLS para o seu servidor pode parar de funcionar. Neste caso, terá de remover a entrada *FrontEndSSLCipherSuiteOrder* do **clusterSettings** e submeter o modelo atualizado do Gestor de Recursos para voltar às definições padrão do pacote de cifra.  Por favor, utilize esta funcionalidade com cuidado.
> 
> 

## <a name="get-started"></a>Introdução
O site de modelo de gestor de recursos Azure Quickstart inclui um modelo com a definição base para criar um Ambiente de Serviço de [Aplicações.](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/)

<!-- LINKS -->

<!-- IMAGES -->
