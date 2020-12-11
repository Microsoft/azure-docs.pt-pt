---
title: Azure AD assegura acesso híbrido com guia de implantação F5 Microsoft Docs
description: Tutorial para implementar F5 BIG-IP Virtual Edition (VE) VM em Azure IaaS para acesso híbrido seguro
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c03009b08dcf33bf4b84bc91232af96e7ba2c71
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095190"
---
# <a name="tutorial-to-deploy-f5-big-ip-virtual-edition-vm-in-azure-iaas-for-secure-hybrid-access"></a>Tutorial para implementar F5 BIG-IP Virtual Edition VM em Azure IaaS para acesso híbrido seguro

Este tutorial acompanha-o até ao fim do processo de implantação da BIG-IP Vitural Edition (VE) em Azure IaaS. No final deste tutorial deve ter:

- Uma máquina virtual BIG-IP (VM) totalmente preparada para modelar uma prova de conceito de acesso híbrido seguro (SHA)

- Uma instância de preparação para usar para testar novas atualizações e hotfixs do sistema BIG-IP

## <a name="prerequisites"></a>Pré-requisitos

A experiência ou conhecimento prévio de F5 BIG-IP não é necessário, no entanto, recomendamos familiarizar-se com [a terminologia F5 BIG-IP.](https://www.f5.com/services/resources/glossary) A implementação de um BIG-IP em Azure para sha requer:

- Uma subscrição paga da Azure ou uma subscrição gratuita de 12 meses de [experiência.](https://azure.microsoft.com/free/)

- Qualquer um dos seguintes SKUs de licença F5 BIG-IP

  - F5 BIG-IP® Melhor pacote

  - F5 BIG-IP Access Policy Manager™ (APM) licença autónoma

  - F5 BIG-IP Access Policy Manager™ (APM) licença de complemento em um BIG-IP F5 BIG-IP existente® Gestor de Tráfego Local™ (LTM)
  
  - Licença de teste de recurso completo BIG-IP de 90 [dias](https://www.f5.com/trial/big-ip-trial.php).

- Um certificado wildcard ou nome alternativo sujeito (SAN) para publicar aplicações web sobre Secure Socket Layer (SSL). [Vamos encriptar](https://letsencrypt.org/) oferece certificado gratuito de 90 dias para testes.

- Um certificado SSL para assegurar a interface de gestão BIG-IPs. Um certificado utilizado para a publicação de aplicações web pode ser usado, se o seu assunto corresponder ao nome de domínio totalmente qualificado (FQDN) da BIG-IP. Por exemplo, um certificado wildcard definido com um assunto *.contoso.com seria adequado para `https://big-ip-vm.contoso.com:8443`

A implementação de VM e os configs do sistema de base demoram cerca de 30 minutos, altura em que a sua plataforma BIG-IP estará pronta para implementar qualquer um dos cenários sha listados [aqui](f5-aad-integration.md).

Para testar os cenários, este tutorial pressupõe que o BIG-IP será implantado num grupo de recursos Azure contendo um ambiente de Diretório Ativo (AD). O ambiente deve ser composto por um Controlador de Domínio (DC) e VMs de anfitrião web (IIS). Ter estes servidores noutras localizações para o BIG-IP VM também é ok, desde que o BIG-IP tenha linha de visão para cada uma das funções necessárias para suportar um determinado cenário. Também são suportados cenários em que o BIG-IP VM está ligado a outro ambiente sobre uma ligação VPN.

Se não tiver os itens mencionados aqui para testes, poderá implantar todo um ambiente de domínio AD em Azure, utilizando este [script](https://github.com/Rainier-MSFT/Cloud_Identity_Lab). Uma recolha de aplicações de teste de amostra também pode ser implantada programáticamente para um anfitrião web IIS usando esta [automatização scripted](https://github.com/jeevanbisht/DemoSuite).

>[!NOTE]
>O [portal Azure](https://portal.azure.com/#home) está em constante evolução, pelo que alguns dos passos neste tutorial podem diferir do layout real observado no portal Azure.

## <a name="azure-deployment"></a>Implantação de azul

Um BIG-IP pode ser implantado em diferentes topologias. Este guia centra-se numa única implementação de interface de rede (NIC). No entanto, se a sua implementação BIG-IP requer múltiplas interfaces de rede para alta disponibilidade, segregação de rede ou mais de 1 GB de produção, considere a utilização dos modelos de [Gestor de Recursos Azure (ARM)](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_multiNIC.html)pré-compilados da F5.

Complete as seguintes tarefas para implantar BIG-IP VE a partir do [Mercado Azure](https://azuremarketplace.microsoft.com/marketplace/apps).

1. Inicie sessão no [portal Azure](https://portal.azure.com/#home) com uma conta, que tem permissões para criar VMs. Por exemplo, Contribuinte

2. No **mercado** do tipo caixa de pesquisa de fita superior, seguido por **Enter**

3. Tipo **F5** no filtro Marketplace, seguido por **Enter**

4. **Selecione + Adicione** da fita superior e do tipo **F5** no filtro do mercado, seguido por **Enter**

5. Selecione **F5 BIG-IP Virtual Edition (BYOL)**  >  **Selecione um plano de software**  >  **F5 BIG-IP VE - ALL (BYOL, 2 Locais de Arranque)**

6. Selecione **Criar**.

![A imagem mostra passos para selecionar um plano de software](./media/f5ve-deployment-plan/software-plan.png)

7. Passe pelo menu **Básicos** e use as seguintes definições

 |  Detalhes do projeto     |  Valor     |
 |:-------|:--------|
 |Subscrição|Subscrição-alvo para a implementação de VM BIG-IP|
 |Grupo de recursos | Grupo de Recursos Azure existentes, o BIG-IP VM será implantado ou criar um. Deve ser o mesmo grupo de recursos dos seus VMs DC e IIS|
 | **Detalhes da instância**|  |
 |Nome da VM| Exemplo BIG-IP-VM |
 |Região | Alvo Azure geo para BIG-IP-VM |
 |Opções de disponibilidade| Só ativa se utilizar VM na produção|
 |Imagem| F5 BIG-IP VE - TODOS (BYOL, 2 Locais de Arranque)|
 |Instância do Azure Spot| Não, mas sinta-se livre para permitir se apropriado |
 |Tamanho| Especificação mínima deve ser 2 vCPUs e 8-Gb memória|
 |**Conta de administrador**|  |
 |Tipo de autenticação|Selecione senha por enquanto. Pode mudar para um par de chaves mais tarde |
 |Nome de utilizador|A identidade que será criada como uma conta local BIG-IP para aceder às suas interfaces de gestão. O nome de utilizador é sensível ao CASO.|
 |Palavra-passe|Acesso de administrador seguro com uma senha forte|
 |**Regras portuárias de entrada**|  |
 |Portas de entrada públicas|Nenhum|

8. Selecione **Seguinte: Discos** deixando todas as predefinições e selecione **Seguinte: Networking**.

9. No menu **Networking,** preencha estas definições.

 |Interface de rede|      Valor |
 |:--------------|:----------------|
 |Rede virtual|O mesmo Azure VNet usado pelos seus VMs DC e IIS, ou criar um|
 |Sub-rede| A mesma sub-rede interna azul que os seus VMs DC e IIS, ou criar um|
 |IP público |  Nenhum|
 |Grupo de Segurança da Rede NIC| Selecione Nenhuma se a sub-rede Azure selecionada nos passos anteriores já estiver associada a um grupo de segurança da Rede (NSG); caso contrário, selecione Básico|
 |Acelerar a rede| Desativado |
 |**Balanceamento de carga**|     |
 |Balanço de carga VM| Não|

10. Selecione **Seguinte: Gestão** e preencha estas definições.

 |Monitorização|    Valor |
 |:---------|:-----|
 |Acompanhamento detalhado| Desativado|
 |Diagnósticos de arranque.|Ativar com conta de armazenamento personalizada. Permite a ligação à interface BIG-IP Secure Shell (SSH) através da opção Consola em Série no portal Azure. Selecione qualquer conta de armazenamento Azure disponível|
 |**Identidade**|  |
 |Identidade gerida atribuída ao sistema|Desativado|
 |Azure Active Directory|Big-IP não suporta atualmente esta opção|
 |**Autoshutdown**|    |
 |Ativar o encerramento automático| Se testar, considere definir o BIG-IP-VM para desligar diariamente|

11. Selecione **Seguinte: Avançado** deixando todas as predefinições e selecione **Seguinte: Tags**.

12. Selecione **Seguinte: Revisão + criar** para rever as suas configurações BIG-IP-VM, antes de selecionar **Criar** para o pontapé da implementação.

13. O tempo para implantar totalmente um BIG-IP VM é normalmente de 5 minutos. Quando estiver concluído, não selecione **Ir para o recurso**, em vez de expandir o menu à esquerda do portal Azure e selecione **grupos** de Recursos para navegar para o seu novo BIG-IP-VM. Se a criação de VM falhar, selecione **Back** and **Next**.

## <a name="network-configuration"></a>Configuração de rede

Quando o BIG-IP VM começa a arrancar, o seu NIC será alojado com um IP privado **primário** emitido pelo serviço Dynamic Host Configuration Protocol (DHCP) da sub-rede Azure a que está ligado. Este IP será utilizado pelo Sistema Operativo de Gestão de Tráfego (TMOS) da BIG-IP para comunicar com:

- Comunicação com outros anfitriões e serviços

- Acesso de saída à internet pública

- Acesso à entrada nas interfaces de gestão de BIG-IPs web config e SSH

Expor qualquer uma destas interfaces de gestão à internet aumenta a superfície de ataque BIG-IPs, daí que o BIG-IPs IP primário não tenha sido a provisionado com um IP público durante a implementação. Em vez disso, será previsto um PI interno secundário e um IP público associado para serviços de publicação.
Este mapeamento de 1 a 1 entre um IP público VM e IP privado permite que o tráfego externo chegue a um VM. No entanto, uma regra Azure NSG também é necessária para permitir o tráfego, da mesma forma que uma firewall.

O diagrama mostra uma única implementação de NIC de um BIG-IP VE em Azure, configurado com um IP primário para operações gerais e gestão, e um servidor virtual separado IPs para serviços de publicação.
Neste arranjo, uma regra NSG permite o tráfego remoto destinado `intranet.contoso.com` a encaminhar para o IP público para o serviço publicado, antes de ser encaminhado para o servidor virtual BIG-IP.

![A imagem mostra a implantação de um único nic ](./media/f5ve-deployment-plan/single-nic-deployment.png) Por padrão, os IPs privados e públicos emitidos para VMs Azure são sempre dinâmicos, pelo que provavelmente mudará em cada reinício de um VM. Evite problemas de conectividade imprevistos alterando o IP de gestão BIG-IPs para estático e faça o mesmo com os IPs secundários utilizados para a publicação de serviços.

1. A partir do menu do seu VM BIG-IP, aceda ao **Networking de Definições**  >  

2. Na vista de networking, selecione o link para o direito da **Interface de Rede**

![A imagem mostra a rede config](./media/f5ve-deployment-plan/network-config.png)

>[!NOTE]
>Os nomes VM são gerados aleatoriamente durante a implantação.

3. No painel de esquerda, selecione **configurações IP** e, em seguida, selecione a linha **ipconfig1**

4. Desajuste a opção **de atribuição de IP** para estática e, se necessário, altere o endereço IP primário de VMs BIG-IP. Em seguida, **selecione Guardar** e fechar o menu ipconfig1

>[!NOTE]
>Você usará este IP primário para ligar e gerir o BIG-IP-VM.

5. **Selecione + Adicione** na fita superior e forneça um nome para um IP privado secundário, por exemplo, ipconfig2

6. Defina a opção de **atribuição** das definições de endereço IP privado para **Estática**. Desde que o próximo IP superior ou inferior consecutivo ajude a manter as coisas ordenadas.

7. Desaponuse o endereço IP público para **Associar** e selecione **Criar**

8. Fornecer um nome para o novo endereço IP público, por exemplo, BIG-IP-VM_ipconfig2_Public

9. Se solicitado, desate o **SKU** para Standard

10. Se solicitado, coloque o **Tier** para **Global** e

11. Desa estale a opção **de atribuição** para **estática,** em seguida, selecione **OK** duas vezes

O seu BIG-IP-VM está agora pronto para configurar com:

- **IP privado primário**: Dedicado à gestão do BIG-IP-VM através da sua utilidade Web config e SSH. Será utilizado pelo sistema BIG-IP como seu **Self-IP** para ligar aos serviços de backend publicados. Além disso, liga-se a serviços externos como NTP, AD e LDAP.

- **IP privado secundário**: Utilizado ao criar um servidor virtual BIG-IP APM para ouvir um pedido de entrada para um(s) de serviços publicados.

- **IP público**: Associado ao IP privado secundário, permite que o tráfego de clientes da internet pública chegue ao servidor virtual BIG-IP para o(s) serviço(s) publicado(s)

O exemplo ilustra a relação 1 a 1 entre um VM vips públicos e privados. Um Azure VM NIC pode ter apenas um IP primário, e quaisquer IPs adicionalmente criados são sempre referidos como secundários.

>[!NOTE]
>Você precisará dos mapeamentos IP secundários para a publicação de serviços BIG-IP.

![Esta imagem mostra todos os IPs secundários](./media/f5ve-deployment-plan/secondary-ips.png)

Para implementar o SHA utilizando a **Configuração Guiada** de Acesso BIG-IP, repita os passos 5-11 para criar pares IP privados e públicos adicionais para cada serviço adicional que pretende publicar através do BIG-IP APM. A mesma abordagem também pode ser utilizada se os serviços de publicação utilizarem BIG-IPs **Configuração Avançada**. No entanto, nesse cenário, tem a opção de evitar sobrecargas ip públicas utilizando uma configuração [de Indicador de Nome do Servidor (SNI).](https://support.f5.com/csp/#/article/K13452) Nesta configuração, um servidor virtual BIG-IP aceitará todo o tráfego de clientes que recebe e encaminha-o para o seu destino.

## <a name="dns-configuration"></a>Configuração do DNS

É essencial ter DNS configurado para os clientes resolverem os seus serviços sha publicados para os IP(s) públicos da BIG-IP-VM.

Os passos seguintes pressupõem que a zona DE DNS do domínio público utilizado para os seus serviços SHA é gerida em Azure. No entanto, os mesmos princípios dns de criação de registo de localizador ainda se aplicam independentemente do local onde a sua zona de DNS seja gerida.

1. Se ainda não estiver aberto, expanda o menu à esquerda do portal e navegue para o seu BIG-IP-VM através da opção **Grupos de Recursos**

2. A partir do menu do seu VM BIG-IP, aceda ao **Networking de Definições**  >  

3. Na vista de rede BIG-IP-VMs, selecione o primeiro IP secundário a partir da lista de configuração IP drop-down e selecione o link para o seu **NIC Public IP**

![Screenshot para mostrar o NIC PÚBLICO IP](./media/f5ve-deployment-plan/networking.png)

4. Abaixo da secção **Definições** no painel de cores do lado esquerdo, selecione **Configuração** para apresentar o menu de propriedades IP e DNS públicos para o IP secundário selecionado

5. Selecione e **crie** registo de pseudónimos e selecione a sua **zona DNS** da lista de drop-down. Se uma zona DE DNS já não existir, então pode ser gerida fora de Azure, ou pode criar uma para o sufixo de domínio que teria verificado em Azure AD.

6. Utilize os seguintes detalhes para criar o primeiro registo de pseudónimos DNS:

 | Campo | Valor |
 |:-------|:-----------|
 |Subscrição| Mesma subscrição do BIG-IP-VM|
 |Zona DNS| A zona de DNS que é autoritária para o sufixo de domínio verificado os seus websites publicados usará, por exemplo, www.contoso.com |
 |Nome | O nome de anfitrião que especifica irá resolver para o IP público que está associado ao IP secundário selecionado. Certifique-se de que define os mapeamentos DNS corretos para IP. Consulte a última imagem na secção de configs de rede, por exemplo, intranet.contoso.com > 13.77.148.215|
 | TTL | 1 |
 |Unidades TTL | Horas |

7. Selecione **Criar** para Azure para guardar essas definições para DNS públicos.

8. Deixe a **etiqueta de nome DNS (opcional)** e selecione **Guardar** antes de fechar o menu IP público.

9. Repita os passos 1 a 6 para criar registos DNS adicionais para cada serviço que pretende publicar usando a Configuração Guiada da BIG-IP.

  Com os registos DNS no local, pode utilizar qualquer uma das ferramentas online, como o [verificador DNS,](https://dnschecker.org/) para verificar se um registo criado se propagou com sucesso em todos os servidores DNS públicos globais.

  Se gerir o seu espaço de nome de domínio DNS utilizando um fornecedor externo como [o GoDaddy,](https://www.godaddy.com/)terá de criar registos utilizando a sua própria unidade de gestão de DNS.

>[!NOTE]
>Também pode utilizar o ficheiro de anfitriões locais de um PC se testar e alternar frequentemente registos DNS. O ficheiro local num PC do Windows pode ser acedido pressionando Win + R no teclado e enviando a palavra **controladores** na caixa de corrida. Basta ter em atenção que um registo local só fornecerá resolução de DNS para o PC local, e não para outros clientes.

## <a name="client-traffic"></a>Tráfego de clientes

Por padrão, os VNets Azure e as sub-redes associadas são redes privadas que não conseguem receber tráfego de Internet. O NIC do SEU BIG-IP-VM deve ser anexado ao NSG especificado durante a implementação. Para que o tráfego web externo chegue ao BIG-IP-VM, tem de definir uma regra NSG de entrada para permitir as portas 443 (HTTPS) e 80 (HTTP) através da internet pública.

1. A partir do **menu** principal do BIG-IP VM, selecione **Networking**

2. **Selecione Adicionar** a regra de entrada para introduzir as seguintes propriedades de regra NSG:

 |     Campo   |   Valor        |
 |:------------|:------------|
 |Origem| Qualquer|
 |Intervalo de portas de origem| *|
 |Endereços IP de destino|Lista separada por vírgula de todos os IPs privados secundários BIG-IP-VM|
 |Portas de destino| 80,443|
 |Protocolo| TCP |
 |Ação| Permitir|
 |Prioridade|Valor disponível mais baixo entre 100 e 4096|
 |Nome | Um nome descritivo, por exemplo: `BIG-IP-VM_Web_Services_80_443`|

3. **Selecione Adicionar** para comprometer as alterações e fechar o menu **de Networking.**

O tráfego HTTP e HTTPS de qualquer local será agora autorizado a chegar a todas as suas interfaces secundárias BIG-IP-VMs. Permitir a porta 80 é útil para permitir que o BIG-IP APM redirecione automaticamente os utilizadores de HTTP para HTTPS. Esta regra pode ser editada para adicionar ou remover IPs de destino, sempre que necessário.

## <a name="manage-big-ip"></a>Gerir BIG-IP

Um sistema BIG-IP é administrado através da sua UI web config, que pode ser acedida usando qualquer um dos seguintes métodos recomendados:

- De uma máquina dentro da rede interna do BIG-IP

- De um cliente VPN ligado à rede interna da BIG-IP-VM

- Publicado via [Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)

Terá de decidir o método mais adequado antes de poder prosseguir com as restantes configurações. Se necessário, pode ligar-se diretamente à configuração web da internet, configurando o IP primário do BIG-IP com um IP público. Em seguida, adicionando uma regra NSG para permitir o tráfego 8443 para o IP primário. Certifique-se de restringir a fonte ao seu próprio IP de confiança, caso contrário qualquer pessoa será capaz de se conectar.

Uma vez pronto, confirme que pode ligar-se à config web do BIG-IP VM e iniciar sessão com as credenciais especificadas durante a implementação de VM:

- Se estiver a ligar-se a partir de um VM na sua rede interna ou via VPN, ligue-se diretamente à BIG-IPs ip primária e à porta web config. Por exemplo, `https://<BIG-IP-VM_Primary_IP:8443`. O seu navegador irá solicitar que a ligação seja insegura, mas pode ignorar o pedido até que o BIG-IP esteja configurado. Se o navegador insistir em bloquear o acesso, limpe a cache e tente novamente.

- Se publicou o web config via Application Proxy, em seguida, use o URL definido para aceder à web config externamente, sem anexar a porta, por exemplo, `https://big-ip-vm.contoso.com` . O URL interno deve ser definido usando a porta web config, por exemplo, `https://big-ip-vm.contoso.com:8443` 

Um sistema BIG-IP também pode ser gerido através do seu ambiente SSH subjacente, que é normalmente utilizado para tarefas de linha de comando (CLI) e acesso ao nível da raiz. Existem várias opções para a ligação ao CLI, incluindo:

- [Serviço Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview): Permite ligações rápidas e seguras a qualquer VM dentro de um vNET, a partir de qualquer local

- Conecte-se diretamente através de um cliente SSH como PuTTY através da abordagem JIT

- Consola em série: Oferecida na parte inferior do menu de Suporte e resolução de problemas do menu VMs no portal. Não suporta transferências de ficheiros.

- Tal como acontece com a configuração web, também pode ligar-se diretamente ao CLI a partir da internet, configurando o IP primário do BIG-IP com um IP público e adicionando uma regra NSG para permitir o tráfego de SSH. Mais uma vez, certifique-se de restringir a fonte ao seu próprio IP de confiança, se utilizar este método.  

## <a name="big-ip-license"></a>Licença BIG-IP

Um sistema BIG-IP deve ser ativado e a provisionado com o módulo APM antes de poder ser configurado para serviços de publicação e SHA.

1. Faça login de volta na web config e na página **de propriedades gerais** selecione **Ativar**

2. No campo **chave de registo de base,** insira a chave sensível ao caso fornecida pela F5

3. Deixe o **Método de Ativação** definido para **Automático** e selecione **Seguinte**, o BIG-IP validará a licença e exibirá o contrato de licença de utilizador final (EULA).

4. **Selecione Aceitar** e esperar que a ativação esteja concluída, antes de selecionar **Continue**.

5. Faça o login de volta e na parte inferior da página de resumo da Licença, selecione **Seguinte**. O BIG-IP irá agora apresentar uma lista de módulos que fornecem as várias funcionalidades necessárias para a SHA. Se não vir isto, a partir do separador principal aceda ao Fornecimento de Recursos **do Sistema.**  >  

6. Consulte a coluna de provisionamento para a Política de Acesso (APM)

![A imagem mostra o fornecimento de acesso](./media/f5ve-deployment-plan/access-provisioning.png)

7. Selecione **Enviar** e aceitar o aviso

8. Tenha paciência e aguarde que o BIG-IP complete a iluminação das novas funcionalidades. Pode pedalar várias vezes antes de ser totalmente inicializado. 

9. Quando estiver pronto **selecione Continue** e a partir do separador Sobressaí-lo  **Selecione Executar o utilitário de configuração**

>[!IMPORTANT]
>As licenças F5 estão limitadas a serem consumidas por uma única instância BIG-IP VE a qualquer momento. Pode haver razões para querer migrar uma licença de um caso para outro, e se isso acontecer, então certifique-se de [revogar](https://support.f5.com/csp/article/K41458656) a sua licença de julgamento no caso ativo antes de desativá-la, caso contrário a licença será permanentemente perdida.

## <a name="provision-big-ip"></a>Provisão BIG-IP

Garantir o tráfego de gestão de e para BIG-IPs web config é primordial. Configure um certificado de gestão de dispositivos para ajudar a proteger o canal web config de compromisso.

1. A partir da barra de navegação à esquerda, vá para **a Gestão de** Certificados de  >  **Gestão** de  >  **Certificados** de Gestão de Certificados de Gestão  >  **de Certificados SSL SSL Certificate**  >  

2. Da lista de down down **do Tipo de Importação,** selecione **PKCS 12(IIS)** e **Escolha Ficheiro**. Localize um certificado web SSL que tenha um nome sujeito ou SAN que cubra o FQDN irá atribuir o BIG-IP-VM nos próximos passos

3. Forneça a palavra-passe para o certificado e, em seguida, **selecione Import**

4. A partir da barra de navegação à esquerda, vá para a Plataforma **do Sistema**  >  

5. Configure o BIG-IP-VM com um nome de anfitrião totalmente qualificado e o fuso horário para o seu ambiente, seguido de **Update**

![A imagem mostra propriedades gerais](./media/f5ve-deployment-plan/general-properties.png)

6. A partir da barra de navegação à esquerda, vá para **o Dispositivo** de  >  **Configuração**  >  **do Sistema**  >  **NTP**

7. Especifique uma fonte ntp fiável e selecione **Adicionar**, seguido por **Update**. Por exemplo, `time.windows.com`

Você precisa agora de um registo DNS para resolver o BIG-IPs FQDN especificado nos passos anteriores, para o seu IP privado primário. Um registo deve ser adicionado ao DNS interno do seu ambiente, ou ao ficheiro local de um PC que será utilizado para ligar à configuração web do BIG-IP. De qualquer forma, o aviso do navegador não deve mais aparecer quando se ligar diretamente à web config. Isto é, não através de Application Proxy ou qualquer outro representante inverso.

## <a name="ssl-profile"></a>Perfil SSL

Como um proxy reverso, um sistema BIG-IP pode funcionar como um simples serviço de encaminhamento, também conhecido como um proxy transparente, ou um proxy completo que participa ativamente em trocas entre clientes e servidores.
Um proxy completo cria duas ligações distintas: uma ligação frontal do cliente TCP juntamente com uma ligação separada do servidor TCP backend, acoplada por uma lacuna suave no meio. Os clientes conectam-se ao ouvinte proxy numa das extremidades, também conhecida como **Servidor Virtual,** e o representante estabelece uma ligação separada e independente ao servidor backend. Isto é bidisal de ambos os lados.
Neste modo de procuração completo, o sistema F5 BIG-IP é capaz de inspecionar o tráfego, pelo que interagir com pedidos e respostas também é possível. Certas funções como o equilíbrio de carga e a otimização do desempenho web, bem como serviços de gestão de tráfego mais avançados, tais como segurança da camada de aplicação, aceleração web, encaminhamento de página e acesso remoto seguro, dependem desta funcionalidade.
Ao publicar serviços baseados em SSL, o processo de desencriptação e encriptação do tráfego entre clientes e serviços de backend é tratado por perfis SSL BIG-IP.

Existem dois tipos de perfis:

- **Cliente SSL**: A forma mais comum de criar um sistema BIG-IP para publicar serviços internos com SSL é criar um perfil SSL do Cliente. Com um perfil SSL do Cliente, um sistema BIG-IP pode desencriptar pedidos de clientes de entrada antes de enviá-los para um serviço a jusante. Em seguida, encripta respostas de backend de saída antes de enviá-las para os clientes.

- **Server SSL**: Para serviços de backend configurados para HTTPS, pode configurar o BIG-IP para utilizar um perfil SSL do Servidor. Com um perfil SSL do Servidor, o BIG-IP reen encripta o pedido do cliente antes de o enviar para o serviço de backend destino. Quando o servidor retorna uma resposta encriptada, o sistema BIG-IP desencripta e reencripta a resposta, antes de enviá-la para o cliente, através do perfil SSL do Cliente configurado.

Fornecendo ambos os perfis SSL do Cliente e do Servidor terão o BIG-IP pré-configurado e pronto para todos os cenários SHA.

1. A partir da barra de navegação à esquerda, vá para **a Gestão de** Certificados de  >  **Gestão** de  >  **Certificados** de Gestão de Certificados de Gestão  >  **de Certificados SSL SSL Certificate**  >  

2. Da lista de down down **do Tipo de Importação,** selecione **PKCS 12(IIS)**

3. Fornecer um nome para o certificado importado, por exemplo,  `ContosoWilcardCert`

4. Selecione **Choose File** para navegar no certificado web SSL que o nome do sujeito corresponde ao sufixo de domínio que planeia utilizar para serviços publicados

5. Forneça a **palavra-passe** para o certificado importado e, em seguida, **selecione Import**

6. A partir da barra de navegação à esquerda, vá a **Perfis de Tráfego Local**  >    >  **SSL**  >  **Cliente** e, em seguida, selecione **Criar**

7. Na página New **Client SSL Profile,** forneça um nome amigo único para o novo perfil SSL do novo cliente e certifique-se de que o perfil dos Pais está definido para **clientes**

![A imagem mostra atualização big-ip](./media/f5ve-deployment-plan/client-ssl.png)

8. Selecione a caixa de verificação de extrema-direita na linha **de corrente de chave de certificado** e selecione **Adicionar**

9. A partir das três listas de drop down, selecione o certificado wildcard que importou sem uma palavra-passe e, em seguida, selecione **Add**  >  **Finished**.

![A imagem mostra atualização big-ip contoso wildcard](./media/f5ve-deployment-plan/contoso-wildcard.png)

10. Repita os passos 6-9 para criar um **perfil de certificado de servidor SSL**. A partir da fita superior, selecione **SSL**  >  **Server**  >  **Create**.

11. Na página **'Perfil SSL' do Novo Servidor,** forneça um nome amigável único para o novo perfil SSL do servidor e certifique-se de que o perfil dos pais está definido para **servidor**

12. Selecione a caixa de verificação de extrema-direita para as linhas Certificado e Chave e da lista de drop-down selecione o seu certificado importado, seguido por **Terminado**.

![A imagem mostra atualização do servidor big-ip todo o perfil](./media/f5ve-deployment-plan/server-ssl-profile.png)

>[!NOTE]
>Não se desespere se não conseguir obter um certificado SSL, pode utilizar os certificados integrados de BIG-IP e SSL do cliente. Basta ver um erro de certificado no navegador.

Um último passo na preparação de um BIG-IP para a SHA é garantir que é capaz de localizar os recursos que publica e também o serviço de diretório em que conta para o SSO. Um BIG-IP tem duas fontes de resolução de nomes, começando com o seu ficheiro local///anfitriões, ou se não for encontrado um registo, o sistema BIG-IP utiliza qualquer serviço DNS com o qual tenha sido configurado. O método de ficheiro dos anfitriões não se aplica aos nós e piscinas APM que utilizam um FQDN.

1. Na configuração web, vá ao **Dispositivo de**  >  **Configuração** do  >  **Sistema**  >  **DNS**

2. Na **Lista de Servidores de Procuração DE DNS**, insira o endereço IP do servidor DNS dos seus ambientes

3. Selecione **Adicionar**  >  **Atualização**

Como um passo separado e opcional, pode considerar uma [configuração LDAP](https://somoit.net/f5-big-ip/authentication-using-active-directory) para autenticar sysadmins BIG-IP contra AD em vez de gerir contas BIG-IP locais.

## <a name="update-big-ip"></a>Atualizar BIG-IP

O seu BIG-IP-VM deve ser atualizado para desbloquear todas as novas funcionalidades abrangidas [pela orientação baseada no cenário.](f5-aad-integration.md) Pode verificar a versão TMOS dos sistemas que paira sobre o seu ponteiro do rato sobre o nome de anfitrião BIG-IPs na parte superior esquerda da página principal. Recomenda-se que esteja a correr v15.x e acima, obtido a partir do download de [F5.](https://downloads.f5.com/esd/productlines.jsp) Utilize a [orientação](https://support.f5.com/csp/article/K34745165) para atualizar o sistema principal OS (TMOS).

Se não for possível atualizar o TMOS principal, considere pelo menos atualizar a Configuração Guiada apenas, utilizando estes passos.

1. A partir do separador principal no big-IP web config ir para **a**  >  **Configuração Guiada de** Acesso

2. Na página **de Configuração Guiada,** selecione **a configuração guiada de upgrade**

![A imagem mostra como atualizar o big-ip](./media/f5ve-deployment-plan/update-big-ip.png)

3. No diálogo **de configuração guiada de atualização,** **escolha o Ficheiro** para carregar o pacote de casos de utilização descarregado e selecione Upload and **Install**

4. Quando a atualização estiver concluída, **selecione Continue**.

## <a name="backup-big-ip"></a>Backup BIG-IP

Com o sistema BIG-IP agora totalmente a provisionado, recomendamos que se faça uma cópia de segurança completa da sua configuração:

1. Ir para **o System**  >  **Archives**  >  **Criar**

2. Forneça um **nome de arquivo** único e permita a **encriptação** com uma palavra-passe

3. Desescrite a opção **Chaves Privadas** para **Incluir** também para apoiar os certificados de dispositivo e SSL

4. Selecione **Terminado** e aguarde que o processo esteja concluído

5. Será apresentado um estado de funcionamento que permita um resultado de reserva. Selecione **OK**

6. Guarde o conjunto de configuração do Utilizador (UCS) arquivar localmente, escolhendo o link da cópia de segurança e selecione **Download**.

Como um passo opcional, também pode fazer uma cópia de segurança de todo o disco do sistema utilizando [imagens Azure](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk), que ao contrário da cópia de segurança da web config forneceria alguma contingência para testes entre versões TMOS, ou voltar para um sistema novo.

```PowerShell
# Install modules
Install-module Az
Install-module AzureVMSnapshots

# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Create Snapshot
New-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>'

#List Snapshots
#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>'

#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot -RemoveOriginalDisk 

```

## <a name="restore-big-ip"></a>Restaurar BIG-IP

Restaurar um BIG-IP segue um procedimento semelhante ao backup e também pode ser usado para configurar configs migratórios entre OS VMs BIG-IP. Devem ser observados detalhes sobre os caminhos de atualização suportados antes de importar uma cópia de segurança.

1. Ir para **o System**  >  **Archives**

2. Escolha o link de uma cópia de segurança que deseja restaurar ou selecione o botão **Upload** para navegar para um arquivo UCS previamente guardado que não está na lista

3. Forneça a palavra-passe para a cópia de segurança e selecione **Restaurar**

```PowerShell
# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Restore Snapshot
Get-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot

```

>[!NOTE]
>No momento da escrita, o cmdlet AzVmSnapshot limita-se a restaurar o instantâneo mais recente, com base na data. As imagens são armazenadas na raiz do grupo de recursos do VM. Esteja ciente de que restaurar as imagens reinicia um VM Azure, por isso, cronometre com cuidado.

## <a name="additional-resources"></a>Recursos adicionais

-   [Redefinir a palavra-passe BIG-IP VE em Azure](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html)
    -   [Redefinir a palavra-passe sem utilizar o portal](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html#reset-the-password-without-using-the-portal)

-   [Alterar o NIC utilizado para a gestão DE VE BIG-IP](https://clouddocs.f5.com/cloud/public/v1/shared/change_mgmt_nic.html)

-   [Sobre rotas numa única configuração NIC](https://clouddocs.f5.com/cloud/public/v1/shared/routes.html)

-   [Microsoft Azure: Waagent](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_waagent.html)

## <a name="next-steps"></a>Passos seguintes

Selecione um [cenário de implementação](f5-aad-integration.md) e inicie a sua implementação.