---
title: Configurar um domínio personalizado em Azure Static Web Apps
description: Aprenda a mapear um domínio personalizado para Azure Static Web Apps
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 578860883a108bba4b4bcd8cd04e8c08f484d474
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92173681"
---
# <a name="setup-a-custom-domain-in-azure-static-web-apps-preview"></a>Configurar um domínio personalizado na Pré-visualização das Aplicações Web Estáticas do Azure

Por predefinição, a Azure Static Web Apps fornece um nome de domínio gerado automaticamente. Este artigo mostra-lhe como mapear um nome de domínio personalizado para uma aplicação Azure Static Web Apps.

## <a name="prerequisites"></a>Pré-requisitos

- Um nome de domínio comprado
- Acesso às propriedades de configuração DNS para o seu domínio

Ao configurar nomes de domínio, os registos "A" são utilizados para mapear domínios de raiz (por exemplo, `example.com` ) para um endereço IP. Os domínios de raiz devem ser mapeados diretamente para um endereço IP, porque a especificação DNS não permite o mapeamento de um domínio para outro.

## <a name="dns-configuration-options"></a>Opções de configuração de DNS

Existem alguns tipos diferentes de configurações DNS disponíveis para uma aplicação.

| Se quiser. | Então |
|--|--|
| Apoio `www.example.com` ou `blog.example.net` | [Mapear um registo CNAME](#map-a-cname-record) |
| Apoio `example.com` | [Configure um domínio de raiz](#configure-a-root-domain) |
| Aponte todos os subdomínios para `www.example.com` | [Mapear um wildcard](#map-a-wildcard-domain) |

## <a name="map-a-cname-record"></a>Mapear um registo CNAME

Um registo CNAME mapeia um domínio para outro. Pode utilizar um registo CNAME para `www.example.com` mapear, `blog.example.com` ou qualquer outro sub-domínio para o domínio gerado automaticamente que é fornecido por Azure Static Web Apps.

1. Abra ao [portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

1. Procure e selecione **Aplicações Web estáticas**

1. Na página _Static Web Apps,_ selecione o nome da sua aplicação.

1. Clique em **domínios personalizados** no menu.

1. Clique no botão **Adicionar**

1. Na janela _de domínios personalizados,_ copie o URL no campo **Valor.**

### <a name="configure-dns-provider"></a>Configure fornecedor DNS

1. Inicie sessão no site do fornecedor do seu domínio.

2. Localize a página para gerir os registos DNS. Cada fornecedor de domínio tem a sua própria interface de registos DNS, por isso, consulte a documentação do fornecedor. Procure áreas do site com os nomes **Nome de Domínio**, **DNS** ou **Gestão de Servidor de Nomes**.

3. Em muitos casos, pode encontrar a página de registos DNS ao procurar uma ligação como **Os meus domínios** nas informações da sua conta. Vá a essa página e, em seguida, procure um link que seja nomeado semelhante ao **ficheiro Zone**, **DNS Records,** ou **configuração Avançada**.

    A captura de ecrã seguinte mostra um exemplo de uma página de registos DNS:

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="Configuração do fornecedor DNS da amostra":::

4. Criar um novo recorde **CNAME** com os seguintes valores...

    | Definição             | Valor                     |
    | ------------------- | ------------------------- |
    | Tipo                | CNAME                     |
    | Anfitrião                | www                       |
    | Valor               | Pasta da sua prancheta |
    | TTL (se aplicável) | Deixe como valor padrão    |

5. Guarde as alterações com o seu fornecedor DNS.

### <a name="validate-cname"></a>Validar CNAME

1. Volte à janela _de domínios personalizados_ no portal Azure.

1. Introduza o seu domínio, incluindo a `www` parte na secção de domínio personalizado _Validate._

1. Clique no botão **Validar.**

Agora que o domínio personalizado está configurado, pode levar várias horas para o fornecedor de DNS propagar as mudanças em todo o mundo. Pode verificar o estado da propagação indo para [dnspropagation.net](https://dnspropagation.net). Introduza o seu domínio personalizado, incluindo o `www` CNAME, selecione CNAME a partir do drop-down, e selecione **Start**.

Se as alterações ao DNS tiverem sido preenchidas, o website devolve o URL gerado automaticamente da sua Aplicação Web Estática (por exemplo, _random-name-123456789c.azurestaticapps.net)._

## <a name="configure-a-root-domain"></a>Configure um domínio de raiz

Os domínios de raiz são o seu domínio menos qualquer subdomínio, incluindo `www` . Por exemplo, o domínio raiz `www.example.com` para é `example.com` . Isto também é conhecido como um domínio "APEX".

Embora o suporte ao domínio de raiz não esteja disponível durante a pré-visualização, pode ver os [domínios de raiz de configuração](https://burkeholland.github.io/posts/static-app-root-domain) do blog em Azure Static Web Apps para obter detalhes sobre como configurar o suporte ao domínio de raiz com uma Aplicação Web Estática.

## <a name="map-a-wildcard-domain"></a>Mapear um domínio com caráteres universais

Às vezes queremos que todo o tráfego seja enviado para um subdomínio para outro domínio. Um exemplo comum é mapear todo o tráfego de subdomínio para `www.example.com` . Desta forma, mesmo que alguém do `w.example.com` tipo em vez `www.example.com` de, o pedido seja enviado para `www.example.com` .

### <a name="configure-dns-provider"></a>Configure fornecedor DNS

1. Inicie sessão no site do fornecedor do seu domínio.

2. Localize a página para gerir os registos DNS. Cada fornecedor de domínio tem a sua própria interface de registos DNS, por isso, consulte a documentação do fornecedor. Procure áreas do site com os nomes **Nome de Domínio**, **DNS** ou **Gestão de Servidor de Nomes**.

3. Em muitos casos, pode encontrar a página de registos DNS ao procurar uma ligação como **Os meus domínios** nas informações da sua conta. Vá a essa página e, em seguida, procure um link chamado similar ao **ficheiro Zone**, **DNS Records,** ou **configuração Avançada**.

    A captura de ecrã seguinte mostra um exemplo de uma página de registos DNS:

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="Configuração do fornecedor DNS da amostra":::

4. Crie um novo registo **CNAME** com os seguintes valores, substituindo `www.example.com` pelo seu nome de domínio personalizado.

    | Definição | Valor                  |
    | ------- | ---------------------- |
    | Tipo    | CNAME                  |
    | Anfitrião    | \*                     |
    | Valor   | www.example.com        |
    | TTL     | Deixe como valor padrão |

5. Guarde as alterações com o seu fornecedor DNS.

Agora que o domínio wildcard está configurado, pode levar várias horas para que as mudanças se propaguem em todo o mundo. Pode verificar o estado da propagação indo para [dnspropagation.net](https://dnspropagation.net). Introduza o seu domínio personalizado de domínio com qualquer subdomínio `www` (diferente), selecione CNAME a partir do drop-down e selecione **Start**.

Se as alterações ao DNS tiverem sido preenchidas, o website devolve o seu domínio personalizado configurado para a sua Aplicação Web Estática (por exemplo, `www.example.com` ).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configurar as definições da aplicação](application-settings.md)
