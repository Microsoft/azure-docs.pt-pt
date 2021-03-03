---
title: Entidades nomeadas para informações pessoais
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/17/2021
ms.author: aahi
ms.openlocfilehash: 97167485dae155670f0eb83fc3ef9cb658952251
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101749968"
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

        Nomes de pessoas. Também devolvido `domain=phi` com.
      
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
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Bolsa de valores

    :::column-end:::
    :::column span="2":::

        Grupos de bolsa. 
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Desportos

    :::column-end:::
    :::column span="2":::

        Organizações relacionadas com o desporto.
      
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
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Cadeia de conexão de base de dados Azure IAAS e cadeia de conexão Azure SQL

    :::column-end:::
    :::column span="2":::

        Cadeia de ligação para uma infraestrutura Azure como base de dados de serviço (IaaS) e cadeia de conexão SQL.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Cadeia de conexão Azure SQL

    :::column-end:::
    :::column span="2":::

        Cadeia de ligação para uma base de dados na Base de Dados Azure SQL.
      
    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Cadeia de conexão Azure IoT  

    :::column-end:::
    :::column span="2":::

        Fio de ligação para Azure IoT. 
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Publicar Senha de Definição  

    :::column-end:::
    :::column span="2":::

        Palavra-passe para as definições de publicação do Azure.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Cadeia de conexão Azure Redis Cache 

    :::column-end:::
    :::column span="2":::

        Fio de ligação para uma cache Redis.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure SAS 

    :::column-end:::
    :::column span="2":::

        Cadeia de ligação para software Azure como serviço (SaaS).
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Cadeia de conexão de ônibus de serviço Azure

    :::column-end:::
    :::column span="2":::

        Fio de ligação para um ônibus de serviço Azure.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Chave da conta de armazenamento Azure 

    :::column-end:::
    :::column span="2":::

       Chave de conta para uma conta de armazenamento Azure. 
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Chave da conta de armazenamento Azure (genérico)

    :::column-end:::
    :::column span="2":::

       Chave de conta genérica para uma conta de armazenamento Azure.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Cadeia de conexão do servidor SQL 

    :::column-end:::
    :::column span="2":::

       Cadeia de ligação para um computador que executa o SQL Server.
      
    :::column-end:::
:::row-end:::

### <a name="identification"></a>Identificação

[!INCLUDE [supported identification entities](./identification-entities.md)]
