---
title: Entidades nomeadas para informações pessoais
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/15/2021
ms.author: aahi
ms.openlocfilehash: 19586c09cca9a0dc74ba9ee4ef9da459964f9b7e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599329"
---
> [!NOTE]
> Para detetar informações de saúde protegidas (PHI), utilize o `domain=phi` parâmetro e a versão do modelo ou `2020-04-01` posteriormente.
>
> Por exemplo: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii?domain=phi&model-version=2021-01-15`
 
As seguintes categorias de entidades são devolvidas quando envia pedidos para o `/v3.1-preview.3/entities/recognition/pii` ponto final.


| Categoria   |  Descrição                          |
|------------|-------------|
| [Pessoa](#category-person)      |  Nomes de pessoas.  |
| [PersonType](#category-persontype) | Tipos de emprego ou funções detidas por uma pessoa. |
| [Número de telefone](#category-phonenumber) |Números de telefone (apenas números de telefone dos EUA e da UE). |
| [Organização](#category-organization) |  Empresas, grupos, organismos governamentais e outras organizações.  |
| [Endereço](#category-address) | Endereços de correio completos.  |
| [E-mail](#category-email) | Endereços de e-mail.   |
| [URL](#category-url) | URLs para sites.  |
| [IP](#category-ip) | Endereços IP de rede.  |
| [DateTime](#category-datetime) | Datas e horas do dia. | 
| [Quantidade](#category-quantity) | Números e quantidades numéricas.  |
| [Informação azul](#azure-information) | Informações identificáveis do Azure, tais como informações de autenticação.  |
| [Identificação](#identification) | Identificação específica financeira e por país.  |

### <a name="category-person"></a>Categoria: Pessoa

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        Pessoa

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Nomes de pessoas. 

        Para obter esta categoria de entidade, adicione `Person` ao `pii-categories` parâmetro. `Person` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    
    :::column span="":::
      **Línguas documentais suportadas**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::

### <a name="category-persontype"></a>Categoria: PersonType

Esta categoria contém a seguinte entidade:


:::row:::
    :::column span="":::
        **Entidade**

        PersonType

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Tipos de emprego ou funções detidas por uma pessoa.

        Para obter esta categoria de entidade, adicione `PersonType` ao `pii-categories` parâmetro. `PersonType` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::

    :::column span="":::
      **Línguas documentais suportadas**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-phonenumber"></a>Categoria: Número de telefone

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Números de telefone (apenas números de telefone dos EUA e da UE). Também devolvido `domain=phi` com.

        Para obter esta categoria de entidade, adicione `PhoneNumber` ao `pii-categories` parâmetro. `PhoneNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::

    :::column span="":::
      **Línguas documentais suportadas**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
   :::column-end:::

:::row-end:::


### <a name="category-organization"></a>Categoria: Organização

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        Organização

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Empresas, grupos políticos, bandas musicais, clubes desportivos, organismos governamentais e organizações públicas. As nacionalidades e religiões não estão incluídas neste tipo de entidade.

        Para obter esta categoria de entidade, adicione `Organization` ao `pii-categories` parâmetro. `Organization` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::

    :::column span="":::
      **Línguas documentais suportadas**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::

:::row-end:::

#### <a name="subcategories"></a>Subcategorias

A entidade desta categoria pode ter as seguintes subcategorias.

:::row:::
    :::column span="":::
        **Subcategoria de entidade**

        Médico    

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Empresas médicas e grupos.

        Para obter esta categoria de entidade, adicione `OrganizationMedical` ao `pii-categories` parâmetro. `OrganizationMedical` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::

    :::column span="":::
      **Línguas documentais suportadas**

      `en`   
      
   :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Bolsa de valores

    :::column-end:::
    :::column span="2":::

        Grupos de bolsa. 

        Para obter esta categoria de entidade, adicione `OrganizationStockExchange` ao `pii-categories` parâmetro. `OrganizationStockExchange` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::

    :::column span="":::

      `en`   
      
   :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Desportos

    :::column-end:::
    :::column span="2":::

        Organizações relacionadas com o desporto.

        Para obter esta categoria de entidade, adicione `OrganizationSports` ao `pii-categories` parâmetro. `OrganizationSports` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::

    :::column span="":::

      `en`   
      
   :::column-end:::

:::row-end:::


### <a name="category-address"></a>Categoria: Endereço

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        Endereço

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Endereço de correio completo.

        Para obter esta categoria de entidade, adicione `Address` ao `pii-categories` parâmetro. `Address` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::

    :::column span="":::
      **Línguas documentais suportadas**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::

:::row-end:::

### <a name="category-email"></a>Categoria: E-mail

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        E-mail

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Endereços de e-mail.
      
        Para obter esta categoria de entidade, adicione `Email` ao `pii-categories` parâmetro. `Email` serão devolvidos na resposta da API se for detetado.

    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::


### <a name="category-url"></a>Categoria: URL

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        URL

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        URLs para sites. 

        Para obter esta categoria de entidade, adicione `URL` ao `pii-categories` parâmetro. `URL` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::

    :::column span="":::
      **Línguas documentais suportadas**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::

:::row-end:::

### <a name="category-ip"></a>Categoria: IP

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        IP

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        endereços IP de rede. 

        Para obter esta categoria de entidade, adicione `IP` ao `pii-categories` parâmetro. `IP` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::

    :::column span="":::
      **Línguas documentais suportadas**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::

### <a name="category-datetime"></a>Categoria: DataTime

Esta categoria contém as seguintes entidades:

:::row:::
    :::column span="":::
        **Entidade**

        DateTime

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Datas e horas do dia. 

        Para obter esta categoria de entidade, adicione `DateTime` ao `pii-categories` parâmetro. `DateTime` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
:::column span="":::
      **Línguas documentais suportadas**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Subcategorias

A entidade desta categoria pode ter as seguintes subcategorias.

:::row:::
    :::column span="":::
        **Subcategoria de entidade**

        Data

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Datas de Calender.

        Para obter esta categoria de entidade, adicione `Date` ao `pii-categories` parâmetro. `Date` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="2":::
      **Línguas documentais suportadas**
      
      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
    :::column-end:::
:::row-end:::

### <a name="category-quantity"></a>Categoria: Quantidade

Esta categoria contém as seguintes entidades:

:::row:::
    :::column span="":::
        **Entidade**

        Quantidade

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Números e quantidades numéricas.

        Para obter esta categoria de entidade, adicione `Quantity` ao `pii-categories` parâmetro. `Quantity` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="2":::
      **Línguas documentais suportadas**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Subcategorias

A entidade desta categoria pode ter as seguintes subcategorias.

:::row:::
    :::column span="":::
        **Subcategoria de entidade**

        Idade

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Idades.

        Para obter esta categoria de entidade, adicione `Age` ao `pii-categories` parâmetro. `Age` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="2":::
        **Línguas documentais suportadas**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="azure-information"></a>Informação azul

Estas categorias de entidades incluem informações identificáveis do Azure, incluindo informações de autenticação e cadeias de conexão. Não voltou com o `domain=phi` parâmetro.

:::row:::
    :::column span="":::
        **Entidade**

        Chave Azure DocumentDB Auth 

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Chave de autorização para um servidor DB Azure Cosmos.   

        Para obter esta categoria de entidade, adicione `AzureDocumentDBAuthKey` ao `pii-categories` parâmetro. `AzureDocumentDBAuthKey` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Cadeia de ligação de base de dados Azure IAAS e cadeia de conexão Azure SQL.
        

    :::column-end:::
    :::column span="2":::

        Cadeia de ligação para uma infraestrutura Azure como base de dados de serviço (IaaS) e cadeia de conexão SQL.

        Para obter esta categoria de entidade, adicione `AzureIAASDatabaseConnectionAndSQLString` ao `pii-categories` parâmetro. `AzureIAASDatabaseConnectionAndSQLString` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Cadeia de conexão Azure IoT  

    :::column-end:::
    :::column span="2":::

        Fio de ligação para Azure IoT. 
      
        Para obter esta categoria de entidade, adicione `AzureIoTConnectionString` ao `pii-categories` parâmetro. `AzureIoTConnectionString` serão devolvidos na resposta da API se for detetado.

    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Publicar Senha de Definição  

    :::column-end:::
    :::column span="2":::

        Palavra-passe para as definições de publicação do Azure.

        Para obter esta categoria de entidade, adicione `AzurePublishSettingPassword` ao `pii-categories` parâmetro. `AzurePublishSettingPassword` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Cadeia de conexão Azure Redis Cache 

    :::column-end:::
    :::column span="2":::

        Fio de ligação para uma cache Redis.

        Para obter esta categoria de entidade, adicione `AzureRedisCacheString` ao `pii-categories` parâmetro. `AzureRedisCacheString` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure SAS 

    :::column-end:::
    :::column span="2":::

        Cadeia de ligação para software Azure como serviço (SaaS).

        Para obter esta categoria de entidade, adicione `AzureSAS` ao `pii-categories` parâmetro. `AzureSAS` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Cadeia de conexão de ônibus de serviço Azure

    :::column-end:::
    :::column span="2":::

        Fio de ligação para um ônibus de serviço Azure.

        Para obter esta categoria de entidade, adicione `AzureServiceBusString` ao `pii-categories` parâmetro. `AzureServiceBusString` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Chave da conta de armazenamento Azure 

    :::column-end:::
    :::column span="2":::

        Chave de conta para uma conta de armazenamento Azure. 

        Para obter esta categoria de entidade, adicione `AzureStorageAccountKey` ao `pii-categories` parâmetro. `AzureStorageAccountKey` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Chave da conta de armazenamento Azure (genérico)

    :::column-end:::
    :::column span="2":::

        Chave de conta genérica para uma conta de armazenamento Azure.

        Para obter esta categoria de entidade, adicione `AzureStorageAccountGeneric` ao `pii-categories` parâmetro. `AzureStorageAccountGeneric` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Cadeia de conexão do servidor SQL 

    :::column-end:::
    :::column span="2":::

        Cadeia de ligação para um computador que executa o SQL Server.

        Para obter esta categoria de entidade, adicione `SQLServerConnectionString` ao `pii-categories` parâmetro. `SQLServerConnectionString` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::

### <a name="identification"></a>Identificação

[!INCLUDE [supported identification entities](./identification-entities.md)]
