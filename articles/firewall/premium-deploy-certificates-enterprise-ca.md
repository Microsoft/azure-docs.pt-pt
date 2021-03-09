---
title: Implementar e configurar certificados enterprise CA para pré-visualização Azure Firewall Premium
description: Saiba como implementar e configurar certificados Enterprise CA para pré-visualização Azure Firewall Premium.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 03/09/2021
ms.author: victorh
ms.openlocfilehash: fba95214a6bbb0482166eab8f77f30911986fbb7
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102525500"
---
# <a name="deploy-and-configure-enterprise-ca-certificates-for-azure-firewall-preview"></a>Implementar e configurar certificados enterprise CA para pré-visualização do Firewall Azure

> [!IMPORTANT]
> O Azure Firewall Premium está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


A pré-visualização Azure Firewall Premium inclui uma funcionalidade de inspeção TLS, que requer uma cadeia de autenticação de certificados. Para implementações de produção, deve utilizar um Enterprise PKI para gerar os certificados que utiliza com o Azure Firewall Premium. Utilize este artigo para criar e gerir um certificado de CA intermédio para pré-visualização Azure Firewall Premium.

Para obter mais informações sobre os certificados utilizados pela Azure Firewall Premium Preview, consulte [os certificados de pré-visualização Azure Firewall Premium](premium-certificates.md).

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Para utilizar uma Empresa CA para gerar um certificado para utilizar com a pré-visualização Azure Firewall Premium, deve ter os seguintes recursos: 

- uma Floresta de Diretório Ativo 
- um Ative Directory Certification Services Root CA com inscrição na Web habilitado 
- um Azure Firewall Premium com política de firewall de nível Premium 
- um cofre de chaves Azure 
- uma Identidade Gerida com Permissões de Leitura para **Certificados e Segredos definidas** na Política de Acesso ao Cofre-Chave 

## <a name="request-and-export-a-certificate"></a>Pedido e exportação de certificado

1. Aceda ao site de inscrição na Web no Root CA, normalmente `https://<servername>/certsrv` e selecione **Solicite um Certificado.**
1. Selecione **Pedido de Certificado Avançado.**
1. Selecione **Criar e Submeter um Pedido a este CA.**
1. Preencha o formulário utilizando o modelo da Autoridade de Certificação Subordinada, conforme mostrado:
1. Envie o pedido e instale o certificado.
1. Assumindo que este pedido é feito a partir de um Servidor do Windows utilizando o Internet Explorer, abra **as Opções de Internet**.
1. Navegue no separador **Conteúdo** e selecione **Certificados.**
1. Selecione o certificado que acabou de ser emitido e, em seguida, **selecione Export**.
1. Selecione **Seguinte** para iniciar o assistente. Selecione **Sim, exporte a chave privada** e, em seguida, selecione **Seguinte**.
1. O formato de ficheiro .pfx é selecionado por predefinição. Desmarcar **Inclua todos os certificados na via de certificação, se possível.** Se exportar toda a cadeia de certificados, o processo de importação para a Azure Firewall falhará.
1. Atribua e confirme uma palavra-passe para proteger a chave e, em seguida, selecione **Next**.
1. Escolha o nome do ficheiro e o local de exportação e, em seguida, selecione **Next**.
1. **Selecione Terminar** e mover o certificado exportado para um local seguro.

## <a name="add-the-certificate-to-a-firewall-policy"></a>Adicione o certificado a uma Política de Firewall

1. No portal Azure, navegue na página de Certificados do seu Cofre-Chave e selecione **Gerar/Importar**.
1. Selecione **Import** como método de criação, nomeie o certificado, selecione o ficheiro .pfx exportado, introduza a palavra-passe e, em seguida, selecione **Criar**.
1. Navegue na página de **inspeção TLS (pré-visualização)** da sua política de Firewall e selecione a sua identidade gerida, cofre de chaves e certificado. 
1. Selecione **Guardar**.
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/tls-inspection.png" alt-text="Inspeção TLS":::

## <a name="validate-tls-inspection"></a>Validar a inspeção TLS

1. Crie uma Regra de Aplicação utilizando a inspeção TLS para o URL de destino ou FQDN à sua escolha.  Por exemplo: `*bing.com`.
1. A partir de uma máquina ligada a domínio dentro da gama Fonte da regra, navegue até ao seu Destino e selecione o símbolo de bloqueio ao lado da barra de endereços no seu navegador. O certificado deve mostrar que foi emitido pela sua Empresa ac em vez de um CA público.
1. Mostre o certificado para apresentar mais detalhes, incluindo o caminho do certificado.
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/certificate-details.png" alt-text="detalhes do certificado":::
1. No Log Analytics, faça a seguinte consulta KQL para devolver todos os pedidos que tenham sido sujeitos à Inspeção TLS:
   ```
   AzureDiagnostics 
   | where ResourceType == "AZUREFIREWALLS" 
   | where Category == "AzureFirewallApplicationRule" 
   | where msg_s contains "Url:" 
   | sort by TimeGenerated desc
   ```
   O resultado mostra o URL completo do tráfego inspecionado: :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/kql-query.png" alt-text="consulta KQL":::

## <a name="next-steps"></a>Passos seguintes

[Pré-visualização premium Azure Firewall no portal Azure](premium-portal.md)
