---
title: Configurar um domínio personalizado em Aplicações Web Estáticas Azure
description: Aprenda a mapear um domínio personalizado para aplicações web estáticas azure
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 8425c2c1f653d874d24053a12d511c64a3b9ee9d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655235"
---
# <a name="setup-a-custom-domain-in-azure-static-web-apps-preview"></a>Configurar um domínio personalizado na pré-visualização de aplicações web estáticas do Azure

Por padrão, as Aplicações Web Estáticas Azure fornecem um nome de domínio gerado automaticamente. Este artigo mostra-lhe como mapear um nome de domínio personalizado para uma aplicação de Web Apps Estáticas Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Um nome de domínio adquirido
- Acesso às propriedades de configuração DNS para o seu domínio

Ao configurar nomes de domínio, os "A" Records são usados para mapear domínios de raiz (por exemplo, `example.com` ) para um endereço IP. Os domínios radiculares devem ser mapeados diretamente para um endereço IP, porque a especificação dNS não permite o mapeamento de um domínio para outro.

## <a name="dns-configuration-options"></a>Opções de configuração DNS

Existem alguns tipos diferentes de configurações de DNS disponíveis para uma aplicação.

| Se quiser.                            | Então                                                |
| ----------------------------------------- | --------------------------------------------------- |
| Apoio`www.example.com`                 | [Mapear um registo CNAME](#map-a-cname-record)           |
| Apoio`example.com`                     | [Configurar um domínio raiz](#configure-a-root-domain) |
| Apontar todos os subdomínios para`www.example.com` | [Mapear um wildcard](#map-a-wildcard-domain)                   |

## <a name="map-a-cname-record"></a>Mapear um registo CNAME

Um registo CNAME mapeia um domínio para outro. Pode utilizar um registo CNAME para mapear `www.example.com` o domínio gerado automaticamente pelas Aplicações Web Estáticas do Azure.

1. Abra ao [portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

1. Pesquisar e selecionar **aplicações web estáticas**

1. Na página _'Aplicações Web Estáticas',_ selecione o nome da sua aplicação.

1. Clique em **domínios personalizados** no menu.

1. Na janela _de domínios Personalizados,_ copie o URL no campo **Valor.**

### <a name="configure-dns-provider"></a>Configure provedor de DNS

1. Inicie sessão no site do fornecedor do seu domínio.

2. Localize a página para gerir os registos DNS. Cada fornecedor de domínio tem a sua própria interface de registos DNS, por isso, consulte a documentação do fornecedor. Procure áreas do site com os nomes **Nome de Domínio**, **DNS** ou **Gestão de Servidor de Nomes**.

3. Em muitos casos, pode encontrar a página de registos DNS ao procurar uma ligação como **Os meus domínios** nas informações da sua conta. Vá a essa página e procure um link que seja nomeado semelhante ao **ficheiro Zone,** **DNS Records**ou **configuração Avançada**.

    A captura de ecrã seguinte mostra um exemplo de uma página de registos DNS:

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="Configuração do fornecedor DNS da amostra":::

4. Crie um novo recorde **CNAME** com os seguintes valores...

    | Definição             | Valor                     |
    | ------------------- | ------------------------- |
    | Tipo                | CNAME                     |
    | Anfitrião                | www                       |
    | Valor               | Pasta da sua prancheta |
    | TTL (se aplicável) | Deixe como valor padrão    |

5. Guarde as alterações com o seu fornecedor DeNS.

### <a name="validate-cname"></a>Validar o CNAME

1. Volte à janela de _domínios personalizados_ no portal Azure.

1. Introduza o seu domínio, incluindo a parte na secção de `www` _domínio personalizado Validar._

1. Clique no botão **Validar.**

Agora que o domínio personalizado está configurado, pode levar várias horas para o fornecedor de DNS propagar que muda em todo o mundo. Pode verificar o estado da propagação [indo](https://dnspropagation.net)para dnspropagation.net . Introduza o seu domínio personalizado, incluindo o `www` CNAME , selecione o **"Drop-down"** e selecione Iniciar .

Se as alterações do DNS tiverem povoado, o website devolve o URL gerado automaticamente pela sua Aplicação Web Estática (por exemplo, _random-name-123456789c.azurestaticapps.net_).

## <a name="configure-a-root-domain"></a>Configurar um domínio raiz

Os domínios das raízes são o seu domínio menos qualquer subdomínio, incluindo `www` . Por exemplo, o domínio raiz para `www.example.com` `example.com` é . Isto também é conhecido como um domínio "APEX".

Embora o suporte ao domínio raiz não esteja disponível durante a pré-visualização, pode ver os domínios raiz do blog [Configure em Web Apps Estáticas do Azure](https://burkeholland.github.io/posts/static-app-root-domain) para obter detalhes sobre como configurar o suporte de domínio raiz com uma Aplicação Web Estática.

## <a name="map-a-wildcard-domain"></a>Mapear um domínio com caráteres universais

Às vezes, queremos que todo o tráfego enviado para um subdomínio se encaminhe para outro domínio. Um exemplo comum é mapear todo o tráfego de subdomínio para `www.example.com` . Desta forma, mesmo que alguém otype `w.example.com` em vez de , o pedido é enviado para `www.example.com` `www.example.com` .

### <a name="configure-dns-provider"></a>Configure provedor de DNS

1. Inicie sessão no site do fornecedor do seu domínio.

2. Localize a página para gerir os registos DNS. Cada fornecedor de domínio tem a sua própria interface de registos DNS, por isso, consulte a documentação do fornecedor. Procure áreas do site com os nomes **Nome de Domínio**, **DNS** ou **Gestão de Servidor de Nomes**.

3. Em muitos casos, pode encontrar a página de registos DNS ao procurar uma ligação como **Os meus domínios** nas informações da sua conta. Vá a essa página e procure um link semelhante ao **ficheiro Zone,** **DNS Records**ou **configuração avançada**.

    A captura de ecrã seguinte mostra um exemplo de uma página de registos DNS:

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="Configuração do fornecedor DNS da amostra":::

4. Crie um novo registo **CNAME** com os seguintes valores, substituindo pelo seu nome de `www.example.com` domínio personalizado.

    | Definição | Valor                  |
    | ------- | ---------------------- |
    | Tipo    | CNAME                  |
    | Anfitrião    | \*                     |
    | Valor   | www.example.com        |
    | TTL     | Deixe como valor padrão |

5. Guarde as alterações com o seu fornecedor DeNS.

Agora que o domínio wildcard está configurado, pode levar várias horas para que as alterações se propaguem em todo o mundo. Pode verificar o estado da propagação [indo](https://dnspropagation.net)para dnspropagation.net . Introduza o seu domínio personalizado de domínio com qualquer subdomínio (que não `www` seja), selecione CNAME a partir da gota-para-baixo e selecione **Iniciar**.

Se as alterações do DNS tiverem povoado, o site devolve o seu domínio personalizado configurado para a sua Aplicação Web Estática (por exemplo, `www.example.com` ).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configurar as definições da aplicação](application-settings.md)
