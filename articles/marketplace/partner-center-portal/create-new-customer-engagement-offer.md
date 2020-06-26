---
title: Criar uma oferta dinâmica 365 para o envolvimento do cliente & PowerApps no mercado comercial da Microsoft
description: Como criar uma nova Oferta dinâmica 365 para o envolvimento do cliente & oferta de PowerApps para listagem ou venda no Azure Marketplace, AppSource ou através do programa Cloud Solution Provider (CSP) no Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 8996335a8d39e4dd9fc79584593fe333b302c8be
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85373442"
---
# <a name="create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Criar uma oferta do Dynamics 365 for Customer Engagement & PowerApps

Este tópico explica como criar uma nova oferta Dynamics 365 para o Envolvimento do Cliente & PowerApps. Todas as aplicações para Dynamics 365 para Envolvimento do Cliente (PowerApps, Vendas, Serviço, Serviço de Projeto e Serviço de Campo) devem passar pelo nosso processo de certificação e apoiar uma experiência experimental. O processo de certificação verifica a sua solução para requisitos padrão, compatibilidade e práticas adequadas. A experiência experimental permite que os utilizadores implementem a sua solução para um ambiente Live Dynamics 365.

Antes de iniciar, [crie uma conta de Mercado Comercial no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) se ainda não o fez. Certifique-se de que a sua conta está inscrita no programa de marketplace comercial.

## <a name="create-a-new-offer"></a>Criar uma nova oferta

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação à esquerda, selecione **Commercial Marketplace**  >  **Overview**.
3. Na página 'Visão Geral', selecione **+ Nova oferta**  >  **Dynamics 365 para Envolvimento do Cliente & PowerApps**.

    ![Ilustra o menu de navegação à esquerda.](./media/new-offer-dynamics-365-customer-engagement-powerapps.png)

> [!NOTE]
> Após a publicação de uma oferta, as edições feitas no Partner Center só aparecem nas montras depois de republicarem a oferta. Certifique-se de que é sempre republicante depois de escorção.

## <a name="new-offer"></a>Nova oferta

Introduza um **ID de oferta**. Este é um identificador único para cada oferta na sua conta.

- Este ID é visível para os clientes no endereço web para a oferta de mercado e modelos de Gestor de Recursos Azure, se aplicável.
- Utilize apenas letras minúsculas e números. Pode incluir hífens e sublinhados, mas sem espaços, e está limitado a 50 caracteres. Por exemplo, se introduzir **a oferta de teste-1,** o endereço web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- O ID da Oferta não pode ser alterado depois de selecionar **Criar**.

Insira **um pseudónimo de Oferta.** Este é o nome usado para a oferta no Partner Center.

- Este nome não é usado no mercado e é diferente do nome da oferta e outros valores mostrados aos clientes.
- Isto não pode ser alterado depois de selecionar **Criar**.

Selecione **Criar** para gerar a oferta e continuar.

## <a name="offer-setup"></a>Configuração de oferta

Siga estes passos para definir a sua oferta.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Como pretende que os potenciais clientes interajam com esta oferta de listagem?

Selecione a opção que gostaria de usar para esta oferta.

#### <a name="get-it-now-free"></a>Obtê-lo agora (grátis)

Enuprova gratuitamente a sua oferta aos clientes, fornecendo um URL válido (a começar por *http* ou *https*) onde podem aceder à sua aplicação.  Por exemplo, `https://contoso.com/my-app`.

#### <a name="free-trial-listing"></a>Ensaio gratuito (listagem)

Enuncia a sua oferta aos clientes com um link para um teste gratuito, fornecendo um URL válido (começando `http` com `https` ou) onde podem obter um julgamento.  Por exemplo, `https://contoso.com/trial/my-app`. Os testes gratuitos de listagem de ofertas são criados, geridos e configurados pelo seu serviço e não têm subscrições geridas pela Microsoft.

> [!NOTE]
> Os tokens que a sua aplicação receberá através do seu link de teste só podem ser utilizados para obter informações do utilizador através do Azure Ative Directory (Azure AD) para automatizar a criação de conta na sua app. As contas da Microsoft não são suportadas para autenticação utilizando este token.

#### <a name="contact-me"></a>Contacte-me

Recolher informações de contacto com o cliente ligando o seu sistema de Gestão de Relacionamento com o Cliente (CRM). O cliente será solicitado permissão para partilhar as suas informações. Estes detalhes do cliente, juntamente com o nome da oferta, ID e fonte de mercado onde encontraram a sua oferta, serão enviados para o sistema CRM que configuraste. Para obter mais informações sobre a configuração do seu CRM, consulte [os leads do Cliente](#customer-leads).

**Selecione Guardar o projeto** antes de continuar.

### <a name="test-drive"></a>Unidade de teste

Um test drive é uma ótima maneira de mostrar a sua oferta a potenciais clientes, dando-lhes a opção de "experimentar antes de comprar", resultando numa maior conversão e na geração de leads altamente qualificados. [Saiba mais sobre test drives](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Para ativar uma unidade de teste durante um período de tempo fixo, selecione a caixa de **verificação de acionamento de test drive.** Para remover o test drive da sua oferta, limpe esta caixa de verificação. Configure o ambiente de test drive na secção [técnica de configuração da unidade de teste](#test-drive-technical-configuration) mais tarde neste tópico.

Para obter informações adicionais, consulte [Test drive a sua oferta no mercado comercial.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)

#### <a name="type-of-test-drive"></a>Tipo de unidade de teste

Selecione entre as seguintes opções:

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** – Um modelo de implementação que contém todos os recursos Azure que compõem a sua solução. Os produtos que se adequam a este cenário utilizam apenas recursos Azure.
- **[Dinâmica 365 para Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** – Microsoft acolhe e mantém o serviço de test drive (incluindo fornecimento e implantação) para um sistema de planeamento de recursos empresariais business Central (finanças, operações, cadeia de fornecimento, CRM, etc.).  
- **[Dinâmica 365 para o Envolvimento do Cliente](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** – a Microsoft acolhe e mantém o serviço de test drive (incluindo fornecimento e implementação) para um sistema de Envolvimento do Cliente (vendas, serviço, serviço de projeto, serviço de campo, etc.).  
- **[Dinâmica 365 para Operações](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** – A Microsoft acolhe e mantém o serviço de test drive (incluindo fornecimento e implantação) para um sistema de planeamento de recursos empresariais de Finanças e Operações (finanças, operações, fabricação, cadeia de fornecimento, etc.). 
- **[Aplicação lógica](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** – Um modelo de implementação que abrange todas as arquiteturas complexas de soluções. Quaisquer produtos personalizados devem utilizar este tipo de Test Drive.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** – Uma ligação incorporada a um dashboard personalizado. Os produtos que pretendam demonstrar um power bi visual interativo devem utilizar este tipo de Test Drive. Tudo o que precisa para carregar aqui é o seu URL de POWER BI incorporado.

#### <a name="additional-test-drive-resources"></a>Recursos adicionais de test drive

- [Melhores Práticas Técnicas](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Marketing Best Practices](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Visão geral dos test drives](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (certifique-se de que o seu bloqueador pop-up está desligado)

### <a name="customer-leads"></a>Ligações ao cliente

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Para obter mais informações, consulte [a visão geral da gestão do chumbo.](./commercial-marketplace-get-customer-leads.md)

**Selecione Guardar o projeto** antes de continuar.

## <a name="properties"></a>Propriedades

Esta página permite definir as categorias e indústrias usadas para agrupar a sua oferta no mercado, a sua versão de aplicação e os contratos legais que suportam a sua oferta.

### <a name="category"></a>Categoria

Selecione pelo menos uma e até três categorias. Estes são usados para colocar a sua oferta nas áreas de pesquisa de mercado apropriadas. Não se esqueça de descrever como a sua oferta suporta estas categorias na descrição da oferta.

### <a name="industry"></a>Setor

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="applicable-dynamics-365-products"></a>Dinâmica aplicável 365 produtos

Selecione todos os produtos Dynamics 365 a que esta oferta se aplica.

### <a name="app-version"></a>Versão da aplicação

Insira o número de versão da sua oferta. Os clientes verão esta versão listada na página de detalhes da oferta. Se estiver apenas a atualizar o número da versão devido a alterações de marketing/descritivos, verifique apenas a caixa **de alteração de Marketing.** Esta opção permite que a oferta ignore as fases de certificação e provisionamento.

### <a name="terms-and-conditions"></a>Termos e condições

Forneça os seus próprios termos e condições legais aqui. Também pode fornecer o endereço onde os seus termos e condições podem ser encontrados. Os clientes serão obrigados a aceitar estes termos antes de poderem experimentar a sua oferta.

**Selecione Guardar o projeto** antes de continuar.

## <a name="offer-listing"></a>Listagem de ofertas

Esta página apresenta os idiomas em que a sua oferta será listada. Atualmente, **inglês (Estados Unidos)** é a única opção disponível.

Defina detalhes do mercado para cada idioma/mercado aqui, como nome de oferta, descrição e imagens. Selecione o nome idioma/mercado para fornecer esta informação.

> [!NOTE]
> A oferta de conteúdo de listagem (como a descrição, documentos, screenshots e termos de utilização) não é necessária para estar em inglês, desde que a descrição da oferta comece com a frase: "Esta aplicação está disponível apenas em [língua não inglesa]." Também é aceitável fornecer um *URL de Link Útil* para oferecer conteúdo em um idioma diferente do utilizado no conteúdo da listagem de Oferta.

Aqui está um exemplo de como as informações de oferta aparecem no Microsoft AppSource (quaisquer preços listados são apenas para fins e não se destinam a refletir custos reais):

:::image type="content" source="media/example-azure-marketplace-d365-customer-engagement.png" alt-text="Ilustra como esta oferta aparece no Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Descrições de chamadas

1. Logótipo
2. Produtos
3. Categorias
4. Endereço de suporte (link)
5. Endereço de termos de utilização (link)
6. Nome da oferta
7. Description
8. Screenshots/vídeos

### <a name="name"></a>Name

O nome que introduzir aqui será mostrado aos clientes como o título da sua listagem de oferta. Este campo é pré-povoado com o texto que inseriu para **o pseudónimo Oferta** quando criou a oferta, mas pode alterar este valor. Este nome pode ser comercializado (e pode incluir símbolos de marca registada ou de direitos autorais). O nome não pode ter mais de 50 caracteres e não pode incluir emojis.

### <a name="short-description"></a>Breve descrição

Forneça uma breve descrição da sua oferta, até 100 caracteres. Esta descrição pode ser usada em resultados de pesquisa no mercado.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>Pesquisar palavras-chave

Pode introduzir opcionalmente até três palavras-chave de pesquisa para ajudar os clientes a encontrar a sua oferta no mercado. Para obter melhores resultados, utilize também estas palavras-chave na sua descrição.

### <a name="products-your-app-works-with"></a>Produtos com os seus produtos funcionam

Se quiser informar os clientes de que a sua aplicação funciona com produtos específicos, insira até três nomes de produtos aqui.

### <a name="support-urls"></a>UrLs de apoio

Esta secção permite-lhe fornecer links para ajudar os clientes a entender mais sobre a sua oferta.

#### <a name="help-link"></a>Ligação de ajuda

Insira o endereço onde os clientes podem saber mais sobre a sua oferta.

#### <a name="privacy-policy-url"></a>URL de política de privacidade

Insira o endereço para a política de privacidade da sua organização. Você é responsável por garantir que a sua aplicação está em conformidade com as leis e regulamentos de privacidade, e por fornecer uma política de privacidade válida.

### <a name="contacts"></a>Contactos

Forneça o nome, e-mail e número de telefone para um **contacto de suporte** e um contacto de **Engenharia.** Esta informação não é mostrada aos clientes, mas estará disponível para a Microsoft e poderá ser fornecida aos parceiros da CSP.

Na secção **de contacto de Suporte,** também deve fornecer o **URL de suporte** onde os parceiros da CSP podem encontrar suporte para a sua oferta.

### <a name="supporting-documents"></a>Documentos comprovativos

Forneça pelo menos um (e até três) documentos de marketing relacionados aqui, tais como livros brancos, brochuras, checklists ou apresentações, em formato PDF.

### <a name="marketplace-images"></a>Imagens do mercado

Forneça logotipos e imagens para a sua oferta. Todas as imagens devem estar em formato PNG.

>[!NOTE]
>Se tiver um problema de upload de ficheiros, certifique-se de que a rede local não bloqueia o https://upload.xboxlive.com serviço utilizado pelo Partner Center.

#### <a name="store-logos"></a>Logotipos da loja

Forneça o logótipo da sua oferta em três tamanhos de pixel:
- **Pequeno** (obrigatório; 48 x 48)
- **Grande** (obrigatório; 216 x 216)
- **Largo** (opcional; 255 x 115)

#### <a name="screenshots"></a>Capturas de ecrã

Adicione imagens que mostram como a sua oferta funciona. Pelo menos uma imagem é necessária, e pode somar até cinco. Todas as imagens devem ser de 1280 x 720 pixels.

#### <a name="videos"></a>Vídeos

Pode, opcionalmente, adicionar até quatro vídeos que demonstrem a sua oferta. Estes vídeos devem ser apresentados no YouTube e/ou Vimeo. Para cada um, insira o nome do vídeo, o seu URL e uma imagem miniatura do vídeo (1280 x 720 pixels)

#### <a name="additional-marketplace-listing-resources"></a>Recursos de listagem de mercado adicionais

[Melhores práticas para anúncios de oferta de mercado](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

**Selecione Guardar o projeto** antes de continuar.

## <a name="availability"></a>Disponibilidade

Esta página permite definir onde e como disponibilizar a sua oferta.

### <a name="markets"></a>Mercados

Especifique os mercados em que a sua oferta deve estar disponível. Para tal, **selecione mercados editar ,** que irão exibir a janela popup **de seleção de mercado.**

Por padrão, não são selecionados mercados, mas tem de selecionar pelo menos um mercado para publicar a sua oferta. **Escolha Selecione tudo** para disponibilizar a sua oferta em todos os mercados possíveis ou selecione os mercados específicos que pretende adicionar.

As suas seleções aqui aplicam-se apenas a novas aquisições; se alguém já tiver a sua app num determinado mercado e mais tarde remover esse mercado, as pessoas que já têm a oferta nesse mercado podem continuar a usá-la, mas nenhum cliente novo nesse mercado poderá receber a sua oferta.

> [!IMPORTANT]
> É da sua responsabilidade cumprir quaisquer requisitos legais locais, mesmo que esses requisitos não estejam listados aqui ou no Partner Center. Mesmo que selecione todos os mercados, leis locais e restrições ou outros fatores podem impedir que determinadas ofertas sejam listadas em alguns países e regiões.

### <a name="preview-audience"></a>Audiência de pré-visualização

Antes de publicar a sua oferta ao vivo para a oferta de mercado mais ampla, primeiro terá de a disponibilizar a um público de **pré-visualização**limitado. Introduza uma **tecla Oculta** (qualquer fio utilizando apenas letras minúsculas e/ou números) aqui. Os membros do seu público de pré-visualização podem usar esta chave de ocultação como um símbolo para visualizar uma pré-visualização da sua oferta no mercado.

Em seguida, quando estiver pronto para disponibilizar a sua oferta e remover a restrição de pré-visualização, terá de remover a **tecla Oculta** e publicar novamente.

**Selecione Guardar o projeto** antes de continuar.

## <a name="technical-configuration"></a>Configuração técnica

Esta página define os detalhes técnicos utilizados para se ligar à sua oferta. Esta ligação permite-nos oferecer a sua oferta para o cliente final se optarem por adquiri-la.

### <a name="base-license-model"></a>Modelo de licença base

O modelo de licença base determina como os clientes são atribuídos a sua aplicação no CrM Admin Center. Selecione **recurso** por exemplo licenciamento baseado ou **Utilizador** se as licenças forem atribuídas uma por inquilino.

### <a name="requires-s2s-outbound-and-crm-secure-store-access"></a>Requer saída S2S e acesso à loja segura de CRM

Verifique esta caixa para ativar a configuração do acesso de saída CRM Secure Store ou Server-to-Server (S2S). Esta funcionalidade requer consideração especializada da Equipa Dinâmica 365 durante a fase de certificação. A Microsoft entrará em contacto consigo para completar os passos adicionais para suportar esta funcionalidade.

### <a name="application-configuration-url"></a>URL de configuração de aplicação

Forneça o URL para a página web de configuração que permite ao cliente configurar a sua aplicação.

### <a name="crm-package"></a>Pacote CRM

No URL do seu campo **de localização do pacote,** insira o URL de uma conta de Armazenamento Azure Blob que contém o pacote CRM carregado .zip file. Inclua uma chave SAS apenas de leitura no URL para que a Microsoft possa recolher o seu pacote para verificação.

> [!IMPORTANT]
> Para evitar um bloco de publicação, certifique-se de que a data de validade no URL do seu armazenamento Blob não expirou. Pode rever a data acedendo à sua política. Recomendamos que o **prazo de expiração** seja pelo menos um mês no futuro.

Selecione a caixa com a etiqueta **Há mais de um pacote crm no meu ficheiro de pacote,** se aplicável. Em caso afirmativo, certifique-se de que inclui todos os pacotes no seu ficheiro .zip.

Para obter informações detalhadas sobre como construir o seu pacote e atualizar a sua estrutura, consulte [o Passo 3: Criar um pacote AppSource para a sua aplicação.](https://docs.microsoft.com/powerapps/developer/common-data-service/create-package-app-appsource)

### <a name="crm-package-availability"></a>Disponibilidade de pacotes CRM

Nesta secção, selecione **+ Adicionar região** para especificar as regiões geográficas em que o seu pacote CRM estará disponível para os clientes. A implantação nas seguintes regiões soberanas requer uma autorização e validação especiais durante o processo de certificação: [Alemanha,](https://docs.microsoft.com/azure/germany/) [Nuvem do Governo dos EUA](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)e TIP.

Por predefinição, o **URL de configuração de aplicação** que introduziu acima será utilizado para cada região. Se preferir, pode introduzir um URL de configuração de aplicação separado para uma ou mais regiões específicas. 

**Selecione Guardar o projeto** antes de continuar.

## <a name="test-drive-technical-configuration"></a>Configuração técnica de unidade de teste

Esta página permite-lhe configurar uma demonstração ("test drive") que permite que os clientes experimentem a sua oferta antes de a comprar. Saiba mais no artigo [O que é Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Para ativar uma unidade de teste, selecione a caixa de verificação de unidade de teste no separador [de configuração 'Oferta'.](#test-drive) Para remover o test drive da sua oferta, limpe esta caixa de verificação.

Saiba mais no artigo [O que é Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Estão disponíveis os seguintes tipos de test drives, cada um com os seus próprios requisitos de configuração técnica:

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Aplicativo lógico](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (configuração técnica não necessária)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Configuração técnica para unidade de teste do Azure Resource Manager

Um modelo de implantação que contém todos os recursos Azure que compõem a sua solução. Os produtos que se adequam a este cenário utilizam apenas recursos Azure. Saiba mais sobre a configuração de uma [unidade de teste Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regiões (necessárias)** – Atualmente existem 26 regiões apoiadas pelo Azure onde o seu test drive pode ser disponibilizado. Normalmente, você vai querer disponibilizar o seu test drive nas regiões onde você antecipa o maior número de clientes, para que eles possam selecionar a região mais próxima para o melhor desempenho. Terá de se certificar de que a sua subscrição pode implementar todos os recursos necessários em cada uma das regiões que está a selecionar.
- **Instâncias** – Selecione o tipo (quente ou frio) e o número de instâncias disponíveis, que serão multiplicadas pelo número de regiões onde a sua oferta está disponível.

    **Quente** – Este tipo de ocorrência é implantado e aguarda acesso por região selecionada. Os clientes podem aceder instantaneamente a casos *quentes* de um test drive, em vez de terem de esperar por uma implementação. A troca é que estes casos estão sempre a funcionar na sua subscrição do Azure, por isso incorrerão num custo de uptime maior. É altamente recomendado ter pelo menos um caso *Hot,* uma vez que a maioria dos clientes não quer esperar por implementações completas, resultando numa entrega no uso do cliente se não houver nenhuma instância *Hot* disponível.

    **Frio** – Este tipo de ocorrência representa o número total de casos que podem ser implantados por região. As instâncias frias requerem que todo o modelo do Gestor de Recursos de Test Drive implemente quando um cliente solicita o test drive, por isso as instâncias *de frio* são muito mais lentas a carregar do que as instâncias *hot.* A troca é que só tem de pagar a duração do test drive, *nem* sempre está a funcionar na sua subscrição Azure como numa instância *Hot.*

- **Test drive Modelo de Gestor de Recursos Azure** – Carreque o .zip contendo o seu modelo de Gestor de Recursos Azure. Saiba mais sobre a criação de um modelo de Gestor de Recursos Azure no artigo de arranque rápido [Criar e implementar modelos de Gestor de Recursos Azure utilizando o portal Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Duração do test drive** (obrigatório) – Introduza o número de horas em que o test drive permanecerá ativo. O testdDrive termina automaticamente após o fim deste período de tempo. Esta duração só pode ser definida em horas inteiras (por exemplo, as horas "2" são válidas; "1,5" não é).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Configuração técnica para unidade de teste Dynamics 365

A Microsoft pode remover a complexidade da configuração de um test drive hospedando e mantendo o fornecimento e implementação de serviço usando este tipo de test drive. A configuração para este tipo de test drive hospedado é a mesma, independentemente de o test drive estar direcionado para um público de Business Central, Customer Engagement ou Operations.

- **Unidades de teste simultâneas máximas** (necessárias) – Desaprova o número máximo de clientes que podem utilizar o seu test drive de uma só vez. Cada utilizador simultâneo consumirá uma licença Dynamics 365 enquanto o test drive estiver ativo, pelo que terá de garantir que tem licenças suficientes para suportar o limite máximo definido. O valor recomendado é 3-5.

- **Duração do test drive** (obrigatório) – Introduza o número de horas em que o test drive permanecerá ativo. Após este tempo, a sessão terminará e deixará de consumir uma das suas licenças. Recomendamos um valor de 2-24 horas dependendo da complexidade da sua oferta. Esta duração só pode ser definida em horas inteiras (por exemplo, as horas "2" são válidas; "1,5" não é). O utilizador pode solicitar uma nova sessão se ficar sem tempo e quiser aceder novamente ao test drive.

- **URL de exemplo** (obrigatório) – O URL onde o cliente iniciará o seu test drive. Tipicamente, o URL da sua instância Dynamics 365 executa a sua aplicação com dados de amostra instalados (por exemplo, `https://testdrive.crm.dynamics.com` ).

- **Exemplo Web API URL** (obrigatório) – Recupere o URL da Web API para a sua instância Dynamics 365, iniciando sessão na sua conta Microsoft 365 e navegando para **Configurações**  >  **Desativação**  >  **Desenvolvimento Recursos Desinsusitados Desemintado Recursos**Web  >  **API (SERVICE Root URL)**), copiar o URL encontrado aqui (por exemplo, `https://testdrive.crm.dynamics.com/api/data/v9.0` ).

- **Nome de função** (obrigatório) – Forneça o nome da função de segurança que definiu no seu test drive Dynamics 365 personalizado. Este nome de função de segurança será atribuído ao utilizador durante o seu test drive (por exemplo, função de test-drive).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Configuração técnica para unidade de teste de aplicativo Logic

Quaisquer produtos personalizados devem usar este tipo de modelo de implementação de test drive, que engloba uma variedade de arquiteturas complexas de soluções. Para mais informações sobre a configuração de unidades de teste da Aplicação Lógica, visite [Operações](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) e [Envolvimento com o Cliente](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) no GitHub.

- **Região** (lista de dropdown necessária e de seleção única) – Atualmente existem 26 regiões apoiadas pelo Azure onde o seu test drive pode ser disponibilizado. Os recursos para a sua aplicação Logic serão implementados na região que selecionar. Se a sua Aplicação Lógica tiver quaisquer recursos personalizados armazenados numa região específica, certifique-se de que a região está selecionada aqui. A melhor maneira de o fazer é implementar totalmente a sua App Lógica localmente na sua subscrição Azure no portal e verificar se funciona corretamente antes de escamar esta seleção.

- **Unidades de teste simultâneas máximas** (necessárias) – Desaprova o número máximo de clientes que podem utilizar o seu test drive de uma só vez. Estes test drives já estão implantados, permitindo que os clientes acedam instantaneamente aos mesmos sem esperar por uma implantação.

- **Duração do test drive** (obrigatório) – Introduza o tempo de duração em que a Unidade de Teste se mantenha ativa, em 4 de horas. A unidade de teste termina automaticamente após o fim deste período de tempo.

- **Nome do grupo de recursos Azure** (obrigatório) – Introduza o nome do [grupo de recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) onde o seu test drive logic App é guardado.

- **Nome da aplicação lógica Azure** (obrigatório) – Introduza o nome da aplicação Logic que atribui a unidade de teste ao utilizador. Esta aplicação Logic deve ser guardada no grupo de recursos Azure acima.

- **Deprovision logic app name** (required) – Introduza o nome da app Logic que desprovisiona a unidade de teste assim que o cliente estiver terminado. Esta aplicação Logic deve ser guardada no grupo de recursos Azure acima.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Configuração técnica não necessária para as unidades de teste Power BI

Os produtos que pretendam demonstrar um power bi visual interativo podem usar um link incorporado para partilhar um dashboard personalizado como o seu test drive, sem necessidade de mais configurações técnicas. Saiba mais sobre a configuração de aplicações de modelo[Power BI.](https://docs.microsoft.com/power-bi/service-template-apps-overview)

### <a name="deployment-subscription-details"></a>Detalhes da subscrição de implementação

Para permitir que a Microsoft implemente o test drive em seu nome, crie e forneça uma subscrição Azure separada e única (não necessária para as unidades de teste Power BI).

- **ID de subscrição Azure** (obrigatório para o Azure Resource Manager e aplicações Lógica) – Introduza o ID de subscrição para garantir o acesso aos serviços de conta Azure para reportar e faturar o uso de recursos. Recomendamos que considere [criar uma subscrição Azure separada](https://docs.microsoft.com/azure/billing/billing-create-subscription) para usar para test drives se ainda não tiver uma. Pode encontrar o seu ID de subscrição Azure iniciando sessão no [portal Azure](https://portal.azure.com/) e navegando no separador **Subscrições** do menu do lado esquerdo. A seleção do separador apresentará o seu ID de subscrição (por exemplo, "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **ID do inquilino da Azure AD** (obrigatório) – Insira o seu ID do inquilino Azure Ative [(AD).](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) Para encontrar este ID, inscreva-se no [portal Azure](https://portal.azure.com/), selecione o separador Ative Directory no menu esquerdo, selecione **Propriedades**, em seguida, procure o número de ID do **diretório** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Você também pode procurar o ID do inquilino da sua organização usando o seu nome de domínio URL em - [https://www.whatismytenantid.com](https://www.whatismytenantid.com) .

- **Nome do inquilino Azure AD** (obrigatório para Dynamic 365) – Insira o seu nome Azure Ative Directory (AD). Para encontrar este nome, inscreva-se no [portal Azure,](https://portal.azure.com/)no canto superior direito o nome do seu inquilino será listado no nome da sua conta.

- **ID da aplicação AD AZure** (obrigatório) – Introduza o ID da aplicação Azure Ative [(AD).](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) Para encontrar este ID, inscreva-se no [portal Azure](https://portal.azure.com/), selecione o separador Ative Directory no menu esquerdo, selecione **as inscrições da App**, em seguida, procure o número de **ID da aplicação** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD app cliente secreto** (obrigatório) – Insira o [segredo do seu cliente](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)de aplicação AZure. Para encontrar este valor, inscreva-se no [portal Azure](https://portal.azure.com/). Selecione o separador **Azure Ative Directory** no menu esquerdo, selecione **as inscrições da App**e, em seguida, selecione a sua aplicação de test drive. Em seguida, selecione **Certificados e segredos**, selecione **Novo segredo de cliente,** introduza uma descrição, selecione **Nunca** em **Expiração,** em seguida, escolha **Adicionar**. Certifique-se de copiar o valor. Não navegue para longe da página antes de copiar o valor ou não terá acesso ao valor.)

### <a name="test-drive-marketplace-listings"></a>Listas de mercado de unidade de teste

A opção **de listagem marketplace** encontrada no separador Test **drive** exibe os idiomas em que o seu test drive está disponível. Atualmente, **o inglês (Estados Unidos)** é o único local disponível. Selecione o nome do idioma para introduzir informações que descrevem a experiência do test drive.

- **Descrição** (obrigatória) – Descreva o seu test drive, o que será demonstrado, objetivos para o utilizador experimentar, funcionalidades a explorar e quaisquer informações relevantes para ajudar o utilizador a determinar se deve adquirir a sua oferta. Podem ser inseridos até 3.000 caracteres de texto neste campo. 

- **Informação de acesso** (necessária para o Azure Resource Manager e unidades de teste lógica) – Explique o que um cliente precisa de saber para aceder e utilizar este test drive. Caminhe por um cenário para usar a sua oferta e exatamente o que o cliente deve saber para aceder a funcionalidades ao longo do test drive. Podem ser inseridos até 10.000 caracteres de texto neste campo.

- **Manual do Utilizador** (obrigatório) – Uma análise aprofundada da sua experiência de test drive. O Manual do Utilizador deve cobrir exatamente o que pretende que o cliente ganhe ao experimentar o test drive e servir de referência para quaisquer questões que possam ter. O ficheiro deve estar em formato PDF e ser nomeado (255 caracteres no máximo) após o upload.

- **Vídeos** (opcional) – Faça upload de vídeos para um site de hospedagem externo referência à link e imagem de miniatura (533 x 324 pixels) aqui. Isto permite ao cliente visualizar um walk-through de informações para ajudá-los a entender melhor o test drive, incluindo como usar com sucesso as características da sua oferta e entender cenários que realçam os seus benefícios.
  - **Nome** (obrigatório)
  - **Endereço** (obrigatório; YouTube ou Vimeo apenas)
  - **Imagem miniatura** (o ficheiro de imagem deve estar no formato PNG e 533 x 324 pixels)

**Selecione Guardar o projeto** antes de continuar.

## <a name="supplemental-content"></a>Conteúdo suplementar

Esta página permite-lhe fornecer informações adicionais sobre a sua oferta para nos ajudar a validar a sua oferta. Esta informação não é mostrada aos clientes ou publicada no mercado.

### <a name="key-usage-scenario"></a>Cenário de utilização chave

Faça upload de um ficheiro PDF que lista os cenários-chave de utilização da sua oferta. Todos os cenários podem ser verificados pela nossa equipa de validação antes de aprovarmos a sua oferta para o mercado.

**Selecione Guardar o projeto** antes de continuar.

## <a name="publish"></a>Publicar

### <a name="submit-offer-to-preview"></a>Enviar oferta para pré-visualização

Depois de ter concluído todas as secções necessárias da oferta, selecione **'Rever' e publicar** no canto superior direito do portal.

Se é a sua primeira vez publicando esta oferta, você pode:

- Consulte o estado de conclusão de cada secção da oferta.
    - **Não foi iniciada** - A secção não foi tocada e deve ser concluída.
    - **Incompleto** - A secção tem erros que precisam de ser corrigidos ou exigem mais informações a serem fornecidas. Volte para a secção(s) e atualize-a.
    - **Completo** - A secção está completa, todos os dados necessários foram fornecidos e não há erros. Todas as secções da oferta devem estar em estado completo antes de poder submeter a oferta.
- Na secção **Notas para certificação,** forneça instruções de teste à equipa de certificação para garantir que a sua aplicação seja testada corretamente, além de quaisquer notas suplementares úteis para a compreensão da sua aplicação.
- Submeta a oferta de publicação selecionando Enviar por ela que **não se possa fazer uma publicação.** Enviaremos um e-mail para informá-lo quando uma versão de pré-visualização da oferta estiver disponível para que você reveja e aprove. Volte ao Partner Center e selecione **Go-live** para a oferta de publicação ao público (ou se uma oferta privada, para o público privado).

## <a name="next-step"></a>Passo seguinte

- [Atualizar uma oferta existente no Marketplace Comercial](./update-existing-offer.md)
