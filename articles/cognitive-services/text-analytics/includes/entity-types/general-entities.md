---
title: Entidades nomeadas em geral
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2021
ms.author: aahi
ms.openlocfilehash: c1ff099dd6dffe06e9707ff23fffd57ae753ab64
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99500088"
---
A funcionalidade NER para Text Analytics devolve as seguintes categorias gerais (não identificativas). por exemplo, ao enviar pedidos para o `/entities/recognition/general` ponto final.


| Categoria | Descrição                          |
|------------|-------------|--------------------------------------|-------------------------------------------------------------|--------------------------------------|
| [Pessoa](#category-person)     | Nomes de pessoas.  |
| [PersonType](#category-persontype) | Tipos de emprego ou funções detidas por uma pessoa. |
| [Localização](#category-location)    | Marcos naturais e humanos, estruturas, características geográficas e entidades geopolíticas |
| [Organização](#category-organization)  | Empresas, grupos políticos, bandas musicais, clubes desportivos, organismos governamentais e organizações públicas.  |
| [Evento](#category-event)  | Eventos históricos, sociais e naturais. |
| [Produto](#category-product) | Objetos físicos de várias categorias. |
| [Habilidade](#category-skill) | Uma capacidade, habilidade ou perícia.  |
| [Endereço](#category-address) | Endereços de correio completos.  |
| [Número de telefone](#category-phonenumber) | Números de telefone. |
| [E-mail](#category-email) | Endereços de e-mail. |
| [URL](#category-url) | URLs para sites. |
| [IP](#category-ip) | Endereços IP de rede. |
| [DateTime](#category-datetime) | Datas e horas do dia. |
| [Quantidade](#category-quantity) | Medições numéricas e unidades. |


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
      
    :::column-end:::
    :::column span="2":::
      **Línguas documentais suportadas**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, <br> `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt`-`pt`, `ru`, `es`, `sv`, `tr`   
      
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

        Tipos de emprego ou funções detidas por uma pessoa
      
    :::column-end:::
    :::column span="2":::
      **Línguas documentais suportadas**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-location"></a>Categoria: Localização

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        Localização

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Marcos naturais e humanos, estruturas, características geográficas e entidades geopolíticas.
      
    :::column-end:::
    :::column span="2":::
      **Línguas documentais suportadas**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Subcategorias

A entidade desta categoria pode ter as seguintes subcategorias.

:::row:::
    :::column span="":::
        **Subcategoria de entidade**

        Entidade Geopolítica (GPE)

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Cidades, países/regiões, estados.
      
    :::column-end:::
    :::column span="2":::
      **Línguas documentais suportadas**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Estrutural

    :::column-end:::
    :::column span="2":::

        Estruturas artificiais. 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Geográfica

    :::column-end:::
    :::column span="2":::

        Características geográficas e naturais como rios, oceanos e desertos.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
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
    :::column span="2":::
      **Línguas documentais suportadas**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
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
    :::column span="2":::
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
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Desportos

    :::column-end:::
    :::column span="2":::

        Organizações relacionadas com o desporto.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-event"></a>Categoria: Evento

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        Evento

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Eventos históricos, sociais e naturais.
      
    :::column-end:::
    :::column span="2":::
      **Línguas documentais suportadas**

      `en`, `es` `fr` , , , , `de` , `it` , `zh-hans` `ja` `ko` e `pt-pt``pt-br`  
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Subcategorias

A entidade desta categoria pode ter as seguintes subcategorias.

:::row:::
    :::column span="":::
        **Subcategoria de entidade**

        Cultural

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Eventos culturais e feriados.
      
    :::column-end:::
    :::column span="2":::
      **Línguas documentais suportadas**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Natural

    :::column-end:::
    :::column span="2":::

        Eventos naturais.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Desportos

    :::column-end:::
    :::column span="2":::

        Eventos desportivos.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-product"></a>Categoria: Produto

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        Produto

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Objetos físicos de várias categorias.
      
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

        Produtos de computação
    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Produtos de computação.
      
    :::column-end:::
    :::column span="2":::
      **Línguas documentais suportadas**

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-skill"></a>Categoria: Competência

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        Habilidade

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Uma capacidade, habilidade ou perícia.
      
    :::column-end:::
    :::column span="2":::
      **Línguas documentais suportadas**

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
      
    :::column-end:::
    :::column span="2":::
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

        Números de telefone (apenas números de telefone dos EUA e da UE).
      
    :::column-end:::
    :::column span="2":::
      **Línguas documentais suportadas**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
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
    :::column span="2":::
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
      
    :::column-end:::
    :::column span="2":::
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
      
    :::column-end:::
    :::column span="2":::
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
      
    :::column-end:::
    :::column span="2":::
      **Línguas documentais suportadas**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

As entidades desta categoria podem ter as seguintes subcategorias

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
    :::column span="2":::
      **Línguas documentais suportadas**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Hora

    :::column-end:::
    :::column span="2":::

        As horas do dia.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Intervalo de Datas

    :::column-end:::
    :::column span="2":::

        Intervalos de data.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Intervalo de Tempo

    :::column-end:::
    :::column span="2":::

        Intervalos de tempo.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Duração

    :::column-end:::
    :::column span="2":::

        As durações.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Definir

    :::column-end:::
    :::column span="2":::

        Set, vezes repetidas.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
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

        Número

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Números.
      
    :::column-end:::
    :::column span="2":::
      **Línguas documentais suportadas**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Percentagem

    :::column-end:::
    :::column span="2":::

        Percentagens
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Números ordinais

    :::column-end:::
    :::column span="2":::

        Números ordinais.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Idade

    :::column-end:::
    :::column span="2":::

        Idades.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Moeda

    :::column-end:::
    :::column span="2":::

        Moedas
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Dimensões

    :::column-end:::
    :::column span="2":::

        Dimensões e medições.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Temperatura

    :::column-end:::
    :::column span="2":::

        As temperaturas.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
