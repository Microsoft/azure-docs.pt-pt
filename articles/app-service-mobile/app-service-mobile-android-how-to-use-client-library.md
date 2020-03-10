---
title: Como usar o SDK para Android
description: Como utilizar o Azure Mobile Apps SDK para Android
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 52e91d900ce0f22862904695ba8adf463219c469
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374227"
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Como utilizar o Azure Mobile Apps SDK para Android

Este guia mostra-lhe como utilizar o Cliente Android SDK para Aplicações Móveis para implementar cenários comuns, tais como:

* Consulta de dados (inserção, atualização e adesão).
* Autenticação.
* Erros de manipulação.
* Personalizando o cliente.

Este guia centra-se no Android SDK do lado do cliente.  Para saber mais sobre os SDKs do lado do servidor para aplicações móveis, consulte [Trabalhar com .NET backend SDK][10] ou [Como usar o SDK de backend Node.js][11].

## <a name="reference-documentation"></a>Documentação de Referência

Você pode encontrar a [referência Javadocs API][12] para a biblioteca de clientes Android no GitHub.

## <a name="supported-platforms"></a>Plataformas Suportadas

O Azure Mobile Apps SDK para Android suporta os níveis de API 19 a 24 (KitKat através Nougat) para fatores de forma de telefone e tablet.  A autenticação, em particular, utiliza uma abordagem de enquadramento web comum para recolher credenciais.  A autenticação do fluxo do servidor não funciona com dispositivos de fator de forma pequena, como relógios.

## <a name="setup-and-prerequisites"></a>Configuração e Pré-requisitos

Complete o tutorial de arranque rápido de [Aplicativos Móveis.](app-service-mobile-android-get-started.md)  Esta tarefa garante que todos os pré-requisitos para o desenvolvimento de Aplicações Móveis Azure foram cumpridos.  O Quickstart também o ajuda a configurar a sua conta e a criar o seu primeiro backend de Aplicação Móvel.

Se decidir não completar o tutorial Quickstart, complete as seguintes tarefas:

* [criar um backend de aplicação móvel][13] para usar com a sua aplicação Android.
* No Android Studio, [atualize os ficheiros de construção gradle.](#gradle-build)
* [Ativar a permissão da Internet.](#enable-internet)

### <a name="gradle-build"></a>Atualizar o ficheiro de construção gradle

Alterar ambos os ficheiros **build.gradle:**

1. Adicione este código ao ficheiro **build.gradle** nível *do Projeto:*

    ```gradle
    buildscript {
        repositories {
            jcenter()
            google()
        }
    }

    allprojects {
        repositories {
            jcenter()
            google()
        }
    }
    ```

2. Adicione este código ao ficheiro **build.gradle** nível da *aplicação Módulo* dentro da etiqueta de *dependências:*

    ```gradle
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    Atualmente a versão mais recente é 3.4.0. As versões suportadas estão listadas [no bintray][14].

### <a name="enable-internet"></a>Ativar a permissão da Internet

Para aceder ao Azure, a sua aplicação deve ter a permissão de INTERNET ativada. Se ainda não estiver ativado, adicione a seguinte linha de código ao seu ficheiro **AndroidManifest.xml:**

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Criar uma Ligação ao Cliente

As Aplicações Móveis Azure fornecem quatro funções à sua aplicação móvel:

* Acesso de dados e Sincronização Offline com um Serviço de Aplicações Móveis Azure.
* Ligue para APIs personalizados escritos com o Servidor de Aplicativos Móveis Azure SDK.
* Autenticação com Autenticação e Autorização do Serviço de Aplicações Azure.
* Registo de Notificação de Push com Centros de Notificação.

Cada uma destas funções requer primeiro que crie um `MobileServiceClient` objeto.  Apenas um `MobileServiceClient` objeto deve ser criado dentro do seu cliente móvel (isto é, deve ser um padrão Singleton).  Para criar um objeto `MobileServiceClient`:

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

O `<MobileAppUrl>` é um fio ou um objeto URL que aponta para o seu backend móvel.  Se estiver a utilizar o Serviço de Aplicações Azure para alojar o seu backend móvel, certifique-se de que utiliza a versão segura `https://` do URL.

O cliente também requer acesso à Atividade ou Contexto - o parâmetro `this` no exemplo.  A construção do MobileServiceClient deve acontecer dentro do método `onCreate()` da Atividade referenciada no ficheiro `AndroidManifest.xml`.

Como uma boa prática, você deve abstrair a comunicação do servidor na sua própria classe (padrão singleton).  Neste caso, deve passar a Atividade dentro do construtor para configurar adequadamente o serviço.  Por exemplo:

```java
package com.example.appname.services;

import android.content.Context;
import com.microsoft.windowsazure.mobileservices.*;

public class AzureServiceAdapter {
    private String mMobileBackendUrl = "https://myappname.azurewebsites.net";
    private Context mContext;
    private MobileServiceClient mClient;
    private static AzureServiceAdapter mInstance = null;

    private AzureServiceAdapter(Context context) {
        mContext = context;
        mClient = new MobileServiceClient(mMobileBackendUrl, mContext);
    }

    public static void Initialize(Context context) {
        if (mInstance == null) {
            mInstance = new AzureServiceAdapter(context);
        } else {
            throw new IllegalStateException("AzureServiceAdapter is already initialized");
        }
    }

    public static AzureServiceAdapter getInstance() {
        if (mInstance == null) {
            throw new IllegalStateException("AzureServiceAdapter is not initialized");
        }
        return mInstance;
    }

    public MobileServiceClient getClient() {
        return mClient;
    }

    // Place any public methods that operate on mClient here.
}
```

Agora pode ligar para `AzureServiceAdapter.Initialize(this);` no método `onCreate()` da sua atividade principal.  Quaisquer outros métodos que necessitem de acesso ao cliente utilizem `AzureServiceAdapter.getInstance();` para obter uma referência ao adaptador de serviço.

## <a name="data-operations"></a>Operações de Dados

O núcleo do Azure Mobile Apps SDK é fornecer acesso aos dados armazenados no SQL Azure no backend da Aplicação Móvel.  Pode aceder a estes dados utilizando classes fortemente dactilografadas (preferidas) ou consultas não digitadas (não recomendadas).  A maior parte desta secção trata da utilização de classes fortemente dactilografadas.

### <a name="define-client-data-classes"></a>Definir classes de dados do cliente

Para aceder aos dados das tabelas Do SQL Azure, defina as classes de dados dos clientes que correspondem às tabelas do backend da Aplicação Móvel. Exemplos neste tópico assumem uma tabela chamada **MyDataTable,** que tem as seguintes colunas:

* ID
* texto
* completo

O objeto do lado do cliente dactilografado correspondente reside num ficheiro chamado **MyDataTable.java:**

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Adicione métodos de getter e setter para cada campo que adicionar.  Se a sua tabela SQL Azure contiver mais colunas, adicionará os campos correspondentes a esta classe.  Por exemplo, se o DTO (objeto de transferência de dados) tiver uma coluna de prioridade superador, então poderá adicionar este campo, juntamente com os seus métodos de getter e setter:

```java
private Integer priority;

/**
* Returns the item priority
*/
public Integer getPriority() {
    return mPriority;
}

/**
* Sets the item priority
*
* @param priority
*            priority to set
*/
public final void setPriority(Integer priority) {
    mPriority = priority;
}
```

Para aprender a criar tabelas adicionais no seu backend de Aplicações Móveis, consulte [Como: Definir um controlador][15] de mesa (.NET backend) ou [Definir Tabelas utilizando um Schema Dinâmico][16] (Node.js backend).

Uma tabela de backend azure Mobile Apps define cinco campos especiais, quatro dos quais estão disponíveis para os clientes:

* `String id`: A identificação globalmente única para o registo.  Como uma boa prática, faça da identificação a representação da Cadeia de um objeto [UUID.][17]
* `DateTimeOffset updatedAt`: A data/hora da última atualização.  O campo updatedAt é definido pelo servidor e nunca deve ser definido pelo seu código de cliente.
* `DateTimeOffset createdAt`: A data/hora em que o objeto foi criado.  O campo createdAt é definido pelo servidor e nunca deve ser definido pelo seu código de cliente.
* `byte[] version`: Normalmente representado como uma corda, a versão também é definida pelo servidor.
* `boolean deleted`: Indica que o registo foi apagado, mas ainda não foi purgado.  Não utilize `deleted` como propriedade na sua classe.

O campo `id` é obrigatório.  O campo `updatedAt` e `version` campo são utilizados para sincronização offline (para sincronização incremental e resolução de conflitos, respectivamente).  O campo `createdAt` é um campo de referência e não é utilizado pelo cliente.  Os nomes são nomes "across-the-wire" das propriedades e não são ajustáveis.  No entanto, pode criar um mapeamento entre o seu objeto e os nomes "across-the-wire" usando a biblioteca [gson.][3]  Por exemplo:

```java
package com.example.zumoappname;

import com.microsoft.windowsazure.mobileservices.table.DateTimeOffset;

public class ToDoItem
{
    @com.google.gson.annotations.SerializedName("id")
    private String mId;
    public String getId() { return mId; }
    public final void setId(String id) { mId = id; }

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;
    public boolean isComplete() { return mComplete; }
    public void setComplete(boolean complete) { mComplete = complete; }

    @com.google.gson.annotations.SerializedName("text")
    private String mText;
    public String getText() { return mText; }
    public final void setText(String text) { mText = text; }

    @com.google.gson.annotations.SerializedName("createdAt")
    private DateTimeOffset mCreatedAt;
    public DateTimeOffset getCreatedAt() { return mCreatedAt; }
    protected void setCreatedAt(DateTimeOffset createdAt) { mCreatedAt = createdAt; }

    @com.google.gson.annotations.SerializedName("updatedAt")
    private DateTimeOffset mUpdatedAt;
    public DateTimeOffset getUpdatedAt() { return mUpdatedAt; }
    protected void setUpdatedAt(DateTimeOffset updatedAt) { mUpdatedAt = updatedAt; }

    @com.google.gson.annotations.SerializedName("version")
    private String mVersion;
    public String getVersion() { return mVersion; }
    public final void setVersion(String version) { mVersion = version; }

    public ToDoItem() { }

    public ToDoItem(String id, String text) {
        this.setId(id);
        this.setText(text);
    }

    @Override
    public boolean equals(Object o) {
        return o instanceof ToDoItem && ((ToDoItem) o).mId == mId;
    }

    @Override
    public String toString() {
        return getText();
    }
}
```

### <a name="create-a-table-reference"></a>Criar uma referência de tabela

Para aceder a uma tabela, crie primeiro um objeto [MobileServiceTable,][8] ligando para o método **getTable** no [MobileServiceClient][9].  Este método tem duas sobrecargas:

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

No código seguinte, o **mClient** é uma referência ao seu objeto MobileServiceClient.  A primeira sobrecarga é usada onde o nome da classe e o nome da mesa são os mesmos, e é o usado no Quickstart:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

A segunda sobrecarga é utilizada quando o nome da mesa é diferente do nome da classe: o primeiro parâmetro é o nome da mesa.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query"></a>Consulta a Mesa de Backend

Primeiro, obtenha uma referência de tabela.  Em seguida, execute uma consulta na referência da mesa.  Uma consulta é qualquer combinação de:

* Uma [cláusula](#filtering)de filtro `.where()`.
* Uma [cláusula](#sorting)de encomenda `.orderBy()`.
* Uma cláusula de [seleção](#selection)de campo `.select()`.
* Um `.skip()` e `.top()` para [os resultados pagedos.](#paging)

As cláusulas devem ser apresentadas na ordem anterior.

### <a name="filter"></a>Resultados de filtragem

A forma geral de uma consulta é:

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

O exemplo anterior devolve todos os resultados (até ao tamanho máximo da página definido pelo servidor).  O método `.execute()` executa a consulta no backend.  A consulta é convertida para uma consulta [V3 OData][19] antes da transmissão para o backend das Aplicações Móveis.  Na receção, o backend das Aplicações Móveis converte a consulta numa declaração SQL antes de executá-la na instância SQL Azure.  Uma vez que a atividade da rede demora algum tempo, o método `.execute()` devolve um [`ListenableFuture<E>`. ][18]

### <a name="filtering"></a>Filtrar dados devolvidos

A seguinte execução de consulta devolve todos os itens da tabela **ToDoItem** onde **completo** é igual a **falso**.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** é a referência à tabela de serviços móveis que criamos anteriormente.

Defina um filtro utilizando a chamada **do** método na referência da tabela. O método **onde** o método é seguido por um método de **campo** seguido por um método que especifica o predicado lógico. Os métodos predicados possíveis incluem **eq** (iguais), **ne** (não igual), **gt** (maior do que), **ge** (maior ou igual a), **lt** (menos do que), **le** (menos ou igual a). Estes métodos permitem comparar os campos de números e cordas com valores específicos.

Pode filtrar em datas. Os seguintes métodos permitem comparar todo o campo de data ou partes da data: **ano,** **mês,** **dia,** **hora,** **minuto,** e **segundo**. O exemplo seguinte adiciona um filtro para itens cuja data de *vencimento* é igual a 2013.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

Os seguintes métodos suportam filtros complexos nos campos de cordas: **arranquesCom,** **extremidadesCom**, **concat**, **subString**, **indexOf**, **substitua,** **toLower,** **toUpper,** **aparador, aparado**e **comprimento**. O exemplo seguinte filtra para linhas de mesa onde a coluna de *texto* começa com "PRI0".

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

Os seguintes métodos de operador são suportados nos campos de números: **adicione**, **sub,** **mul,** **div,** **mod,** **chão,** **teto**e **redondo.** Os filtros de exemplo seguinte seguem para as filas de mesa onde a **duração** é um número uniforme.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

Pode combinar predicados com estes métodos lógicos: **e,** **ou** **não.** O exemplo que se segue combina dois dos exemplos anteriores.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

Grupo e operadores lógicos de ninhos:

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013)
    .and(
        startsWith("text", "PRI0")
        .or()
        .field("duration").gt(10)
    )
    .execute().get();
```

Para uma discussão mais detalhada e exemplos de filtragem, consulte [Explorar a riqueza do modelo de consulta de clientes Android.][20]

### <a name="sorting"></a>Ordenar dados devolvidos

O código seguinte devolve todos os itens de uma tabela de **ToDoItems** classificados ascendentes pelo campo de *texto.* *mToDoTable* é a referência à tabela de backend que criou anteriormente:

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

O primeiro parâmetro do método **orderBy** é uma cadeia igual ao nome do campo para classificar. O segundo parâmetro utiliza a enumeração **Da Ordem** de Consulta para especificar se deve classificar a subida ou a descida.  Se estiver a filtrar utilizando o método ***onde*** o método, o método ***onde*** o método deve ser invocado antes do método ***orderBy.***

### <a name="selection"></a>Selecione colunas específicas

O código que se segue ilustra como devolver todos os itens de uma tabela de **ToDoItems,** mas apenas exibe os campos **completos** e **de texto.** **mToDoTable** é a referência à tabela de backend que criamos anteriormente.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

Os parâmetros da função selecionada são os nomes de cordas das colunas da tabela que pretende devolver.  O método **selecionado** precisa de seguir métodos como **onde** e **encomendarBy**. Pode ser seguido por métodos de paging como **skip** e **top**.

### <a name="paging"></a>Devolver dados em páginas

Os dados são **sempre** devolvidos nas páginas.  O número máximo de registos devolvidos é definido pelo servidor.  Se o cliente solicitar mais registos, o servidor devolve o número máximo de registos.  Por predefinição, o tamanho máximo da página no servidor é de 50 registos.

O primeiro exemplo mostra como selecionar os cinco principais itens de uma mesa. A consulta devolve os itens de uma tabela de **ToDoItems**. **mToDoTable** é a referência à tabela de backend que criou anteriormente:

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

Aqui está uma consulta que ignora os primeiros cinco itens, e depois devolve os próximos cinco:

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Se desejar obter todos os registos numa tabela, implemente código para iterar em todas as páginas:

```java
List<MyDataModel> results = new ArrayList<>();
int nResults;
do {
    int currentCount = results.size();
    List<MyDataModel> pagedResults = mDataTable
        .skip(currentCount).top(500)
        .execute().get();
    nResults = pagedResults.size();
    if (nResults > 0) {
        results.addAll(pagedResults);
    }
} while (nResults > 0);
```

Um pedido para todos os registos que utilizem este método cria um mínimo de dois pedidos para o backend das Aplicações Móveis.

> [!TIP]
> Escolher o tamanho certo da página é um equilíbrio entre o uso da memória enquanto o pedido está a acontecer, o uso da largura de banda e o atraso na receção dos dados completamente.  A predefinição (50 registos) é adequada para todos os dispositivos.  Se operar exclusivamente em dispositivos de memória maiores, aumente até 500.  Constatámos que o aumento do tamanho da página para além dos 500 registos resulta em atrasos inaceitáveis e grandes problemas de memória.

### <a name="chaining"></a>Como: Métodos de consulta concatenados

Os métodos utilizados na consulta de mesas de backend podem ser concatenados. Os métodos de consulta de corrente permitem selecionar colunas específicas de linhas filtradas que são ordenadas e páginadas. Pode criar filtros lógicos complexos.  Cada método de consulta devolve um objeto de consulta. Para acabar com a série de métodos e executar a consulta, ligue para o método **de execução.** Por exemplo:

```java
List<ToDoItem> results = mToDoTable
        .where()
        .year("due").eq(2013)
        .and(
            startsWith("text", "PRI0").or().field("duration").gt(10)
        )
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .skip(200).top(100)
        .execute()
        .get();
```

Os métodos de consulta acorrentados devem ser ordenados da seguinte forma:

1. Filtrar **(onde)** métodos.
2. Métodos de triagem **(orderBy).**
3. Métodos de seleção **(selecione).**
4. métodos de paging **(saltar** e **em cima).**

## <a name="binding"></a>Ligar dados à interface do utilizador

A ligação de dados envolve três componentes:

* A fonte de dados
* O layout do ecrã
* O adaptador que une os dois.

No nosso código de amostra, devolvemos os dados da tabela **ToDoItem** de Aplicações Móveis SQL Azure numa matriz. Esta atividade é um padrão comum para aplicações de dados.  As consultas de base de dados muitas vezes devolvem uma coleção de linhas que o cliente obtém numa lista ou matriz. Nesta amostra, a matriz é a fonte de dados.  O código especifica um layout de ecrã que define a visão dos dados que aparecem no dispositivo.  Os dois estão ligados juntamente com um adaptador, que neste código é uma extensão do **ArrayAdapter&lt;ToDoItem&gt;** classe.

#### <a name="layout"></a>Definir o Layout

O layout é definido por vários excertos do código XML. Dado um layout existente, o seguinte código representa o **ListView** que queremos povoar com os nossos dados do servidor.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

No código anterior, o atributo de *listitem* especifica o id do layout para uma linha individual na lista. Este código especifica uma caixa de verificação e o seu texto associado e é instantâneo uma vez para cada item da lista. Este layout não mostra o campo **id,** e um layout mais complexo especificaria campos adicionais no visor. Este código está no ficheiro **row_list_to_do.xml.**

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">
    <CheckBox
        android:id="@+id/checkToDoItem"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/checkbox_text" />
</LinearLayout>
```

#### <a name="adapter"></a>Defina o adaptador
Uma vez que a fonte de dados da nossa visão é um conjunto de **ToDoItem,** subclassificamos o nosso adaptador de um **ArrayAdapter&lt;ToDoItem&gt;** classe. Esta subclasse produz uma vista para cada **ToDoItem** usando o layout **row_list_to_do.**  No nosso código, definimos a seguinte classe que é uma extensão do **ArrayAdapter&lt;classe E&gt;:**

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

Sobrepor-se ao método **getView** dos adaptadores. Por exemplo:

```java
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }
        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });
        return row;
    }
```

Criamos uma instância desta classe na nossa Atividade da seguinte forma:

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

O segundo parâmetro do construtor ToDoItemAdapter é uma referência ao layout. Podemos agora instantaneamente o **ListView** e atribuir o adaptador ao **ListView**.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-adapter"></a>Utilize o adaptador para ligar à UI

Está agora pronto para utilizar a ligação de dados. O código seguinte mostra como colocar itens na mesa e enche o adaptador local com os itens devolvidos.

```java
    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
        runAsyncTask(task);
    }
```

Ligue para o adaptador sempre que modificar a tabela **ToDoItem.** Uma vez que as modificações são feitas de forma recorde, lida-se com uma única linha em vez de uma coleção. Quando inserir um item, ligue para o método **de adição** no adaptador; ao eliminar, ligue para o método **de remoção.**

Pode encontrar um exemplo completo no [Android Quickstart Project][21].

## <a name="inserting"></a>Insira os dados no backend

Instantifique instantaneamente uma instância da classe *ToDoItem* e desloque as suas propriedades.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Em seguida, utilize **a inserção()** para inserir um objeto:

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

A entidade devolvida corresponde aos dados inseridos na tabela de backend, incluindo o ID e quaisquer outros valores (como os campos `createdAt`, `updatedAt`e `version`) definidos no backend.

As tabelas de Aplicativos Móveis requerem uma coluna de chave primária chamada **id**. Esta coluna deve ser uma corda. O valor padrão da coluna de IDENTIFICAÇÃO é um GUID.  Pode fornecer outros valores únicos, tais como endereços de e-mail ou nomes de utilizador. Quando não é fornecido um valor de ID de cadeia para um registo inserido, o backend gera um novo GUID.

Os valores de ID de cadeia fornecem as seguintes vantagens:

* As iDs podem ser geradas sem fazer uma ida e volta à base de dados.
* Os registos são mais fáceis de fundir a partir de diferentes tabelas ou bases de dados.
* Os valores de ID integram-se melhor com a lógica de uma aplicação.

Os valores de ID de cadeia são **necessários** para suporte de sincronização offline.  Não é possível alterar um ID uma vez que esteja armazenado na base de dados de backend.

## <a name="updating"></a>Atualizar dados numa aplicação móvel

Para atualizar os dados numa tabela, passe o novo objeto para o método **de atualização()**

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

Neste exemplo, o *item* é uma referência a uma linha na tabela *ToDoItem,* que teve algumas alterações feitas.  A linha com o mesmo **id** é atualizada.

## <a name="deleting"></a>Eliminar dados numa aplicação móvel

O código que se segue mostra como eliminar dados de uma tabela, especificando o objeto de dados.

```java
mToDoTable
    .delete(item);
```

Também pode eliminar um item especificando o campo **id** da linha para eliminar.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="lookup"></a>Procure um item específico por Id

Procure um item com um campo **de identificação** específico com o método **lookUp():**

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="untyped"></a>Como: Trabalhar com dados não digitados

O modelo de programação não digitado dá-lhe controlo exato sobre a serialização da JSON.  Existem alguns cenários comuns em que pode querer utilizar um modelo de programação não digitado. Por exemplo, se a sua tabela de backend contém muitas colunas e só precisa de referir um subconjunto das colunas.  O modelo dactilografado requer que defina todas as colunas definidas no backend das Aplicações Móveis na sua classe de dados.  A maioria das chamadas da API para aceder a dados são semelhantes às chamadas de programação dactilografadas. A principal diferença é que no modelo não typed invoca métodos no objeto **MobileServiceJsonTable,** em vez do objeto **MobileServiceTable.**

### <a name="json_instance"></a>Criar uma instância de uma mesa não typed

Semelhante ao modelo dactilografado, começa-se por obter uma referência de tabela, mas neste caso é um objeto **MobileServicesJsonTable.** Obtenha a referência, ligando para o método **getTable** numa instância do cliente:

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

Depois de ter criado uma instância do **MobileServiceJsonTable,** tem praticamente a mesma API disponível como com o modelo de programação dactilografado. Em alguns casos, os métodos tomam um parâmetro não digitado em vez de um parâmetro dactilografado.

### <a name="json_insert"></a>Insira numa tabela não typed
O código que se segue mostra como fazer uma inserção. O primeiro passo é criar um [JsonObject,][1]que faz parte da biblioteca [gson.][3]

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Em seguida, utilize **a inserção()** para inserir o objeto não digitado na mesa.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Se precisar de obter a identificação do objeto inserido, utilize o método **getAsJsonPrimitive() ()**

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="json_delete"></a>Excluir de uma mesa não typed
O código que se segue mostra como eliminar uma instância, neste caso, a mesma instância de um **JsonObject** que foi criado no exemplo de *inserção* anterior. O código é o mesmo que com o caso dactilografado, mas o método tem uma assinatura diferente uma vez que refere um **JsonObject**.

```java
mToDoTable
    .delete(insertedItem);
```

Também pode eliminar uma instância diretamente utilizando o seu ID:

```java
mToDoTable.delete(ID);
```

### <a name="json_get"></a>Volte todas as linhas de uma mesa não digitada
O código que se segue mostra como recuperar uma mesa inteira. Uma vez que está a usar uma tabela JSON, só pode recuperar seletivamente algumas das colunas da mesa.

```java
public void showAllUntyped(View view) {
    new AsyncTask<Void, Void, Void>() {
        @Override
        protected Void doInBackground(Void... params) {
            try {
                final JsonElement result = mJsonToDoTable.execute().get();
                final JsonArray results = result.getAsJsonArray();
                runOnUiThread(new Runnable() {

                    @Override
                    public void run() {
                        mAdapter.clear();
                        for (JsonElement item : results) {
                            String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                            String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                            Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                            ToDoItem mToDoItem = new ToDoItem();
                            mToDoItem.setId(ID);
                            mToDoItem.setText(mText);
                            mToDoItem.setComplete(mComplete);
                            mAdapter.add(mToDoItem);
                        }
                    }
                });
            } catch (Exception exception) {
                createAndShowDialog(exception, "Error");
            }
            return null;
        }
    }.execute();
}
```

O mesmo conjunto de métodos de filtragem, filtragem e paging disponíveis para o modelo dactilografado estão disponíveis para o modelo não digitado.

## <a name="offline-sync"></a>Implementar sincronização offline

O Azure Mobile Apps Client SDK também implementa sincronização offline de dados usando uma base de dados SQLite para armazenar uma cópia dos dados do servidor localmente.  As operações realizadas numa tabela offline não requerem conectividade móvel para funcionar.  Sincronização offline ajuda na resiliência e desempenho em detrimento de uma lógica mais complexa para a resolução de conflitos.  O Cliente SDK de Aplicações Móveis Azure implementa as seguintes funcionalidades:

* Incremental Sync: Apenas os discos atualizados e novos são descarregados, poupando largura de banda e consumo de memória.
* Concurrency otimista: As operações são assumidas como tendo sucesso.  A Resolução de Conflitos é adiada até que as atualizações sejam realizadas no servidor.
* Resolução de Conflitos: O SDK deteta quando foi feita uma alteração conflituosa no servidor e fornece ganchos para alertar o utilizador.
* Eliminação suave: Os registos apagados são marcados apagados, permitindo que outros dispositivos atualizem a sua cache offline.

### <a name="initialize-offline-sync"></a>Inicializar sincronização offline

Cada tabela offline deve ser definida na cache offline antes da utilização.  Normalmente, a definição de tabela é feita imediatamente após a criação do cliente:

```java
AsyncTask<Void, Void, Void> initializeStore(MobileServiceClient mClient)
    throws MobileServiceLocalStoreException, ExecutionException, InterruptedException
{
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>() {
        @Override
        protected void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                if (syncContext.isInitialized()) {
                    return null;
                }
                SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "offlineStore", null, 1);

                // Create a table definition.  As a best practice, store this with the model definition and return it via
                // a static method
                Map<String, ColumnDataType> toDoItemDefinition = new HashMap<String, ColumnDataType>();
                toDoItemDefinition.put("id", ColumnDataType.String);
                toDoItemDefinition.put("complete", ColumnDataType.Boolean);
                toDoItemDefinition.put("text", ColumnDataType.String);
                toDoItemDefinition.put("version", ColumnDataType.String);
                toDoItemDefinition.put("updatedAt", ColumnDataType.DateTimeOffset);

                // Now define the table in the local store
                localStore.defineTable("ToDoItem", toDoItemDefinition);

                // Specify a sync handler for conflict resolution
                SimpleSyncHandler handler = new SimpleSyncHandler();

                // Initialize the local store
                syncContext.initialize(localStore, handler).get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>Obtenha uma referência à tabela de cache offline

Para uma mesa online, usa `.getTable()`.  Para uma tabela offline, utilize `.getSyncTable()`:

```java
MobileServiceSyncTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

Todos os métodos disponíveis para tabelas online (incluindo filtragem, triagem, paging, inserção de dados, atualização de dados e apagando dados) funcionam igualmente bem em tabelas online e offline.

### <a name="synchronize-the-local-offline-cache"></a>Sincronizar a cache offline local

A sincronização está no controlo da sua aplicação.  Aqui está um método de sincronização exemplo:

```java
private AsyncTask<Void, Void, Void> sync(MobileServiceClient mClient) {
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
        @Override
        protected Void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                syncContext.push().get();
                mToDoTable.pull(null, "todoitem").get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

Se um nome de consulta for fornecido ao método `.pull(query, queryname)`, então a sincronização incremental é usada para devolver apenas registos que tenham sido criados ou alterados desde a última retirada concluída com sucesso.

### <a name="handle-conflicts-during-offline-synchronization"></a>Lidar com conflitos durante a sincronização offline

Se um conflito ocorrer durante uma operação `.push()`, um `MobileServiceConflictException` é lançado.   O item emitido pelo servidor está incorporado na exceção e pode ser recuperado por `.getItem()` na exceção.  Ajuste o impulso ligando para os seguintes itens no objeto MobileServiceSyncContext:

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

Uma vez marcados todos os conflitos como desejar, chame `.push()` novamente para resolver todos os conflitos.

## <a name="custom-api"></a>Ligue para uma API personalizada

Um API personalizado permite-lhe definir pontos finais personalizados que expõem a funcionalidade do servidor que não mapeia para uma operação de inserção, atualização, eliminação ou leitura. Ao utilizar um API personalizado, pode ter mais controlo sobre as mensagens, incluindo ler e definir cabeçalhos de mensagem HTTP e definir um formato de corpo de mensagem diferente do JSON.

De um cliente Android, você liga para o método **invokeApi** para chamar o ponto final personalizado da API. O exemplo que se segue mostra como chamar um ponto final da API chamado **completeAll**, que devolve uma classe de coleção chamada **MarkAllResult**.

```java
public void completeItem(View view) {
    ListenableFuture<MarkAllResult> result = mClient.invokeApi("completeAll", MarkAllResult.class);
    Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
        @Override
        public void onFailure(Throwable exc) {
            createAndShowDialog((Exception) exc, "Error");
        }

        @Override
        public void onSuccess(MarkAllResult result) {
            createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
            refreshItemsFromTable();
        }
    });
}
```

O método **invocado Api** é chamado ao cliente, que envia um pedido post para a nova API personalizada. O resultado devolvido pela API personalizada é apresentado num diálogo de mensagem, assim como quaisquer erros. Outras versões do **invokeApi** permitem-lhe enviar opcionalmente um objeto no organismo de pedido, especificar o método HTTP e enviar parâmetros de consulta com o pedido. Versões não typeizadas de **invokeApi** também são fornecidas.

## <a name="authentication"></a>Adicione a autenticação à sua aplicação

Os tutoriais já descrevem detalhadamente como adicionar estas funcionalidades.

O App Service suporta [a autenticação de utilizadores](app-service-mobile-android-get-started-users.md) de aplicações utilizando vários fornecedores de identidade externa: Facebook, Google, Microsoft Account, Twitter e Azure Ative Directory. Pode definir permissões nas tabelas para restringir o acesso a operações específicas apenas a utilizadores autenticados. Também pode utilizar a identidade dos utilizadores autenticados para implementar regras de autorização no seu backend.

São suportados dois fluxos de autenticação: um fluxo de **servidor** e um fluxo de **cliente.** O fluxo do servidor proporciona a experiência de autenticação mais simples, uma vez que se baseia na interface web dos fornecedores de identidade.  Não são necessários SDKs adicionais para implementar a autenticação do fluxo do servidor. A autenticação do fluxo do servidor não proporciona uma integração profunda no dispositivo móvel e é recomendada apenas para prova de cenários de conceito.

O fluxo do cliente permite uma integração mais profunda com capacidades específicas do dispositivo, tais como um único sign-on, uma vez que depende de SDKs fornecidos pelo fornecedor de identidade.  Por exemplo, pode integrar o SDK do Facebook na sua aplicação móvel.  O cliente móvel troca na aplicação do Facebook e confirma a sua inscrição antes de trocar de novo para a sua aplicação móvel.

São necessários quatro passos para permitir a autenticação na sua aplicação:

* Registe a sua aplicação para autenticação junto de um fornecedor de identidade.
* Configure o seu backend do Serviço de Aplicações.
* Restringir as permissões de tabela a utilizadores autenticados apenas no backend do Serviço de Aplicações.
* Adicione código de autenticação à sua aplicação.

Pode definir permissões nas tabelas para restringir o acesso a operações específicas apenas a utilizadores autenticados. Também pode utilizar o SID de um utilizador autenticado para modificar pedidos.  Para mais informações, [Começar com a autenticação] e a documentação do Server SDK HOWTO.

### <a name="caching"></a>Autenticação: Fluxo de servidor

O código seguinte inicia um processo de login de fluxo do servidor utilizando o fornecedor da Google.  É necessária uma configuração adicional devido aos requisitos de segurança para o fornecedor da Google:

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

Além disso, adicione o seguinte método à classe principal de Atividade:

```java
// You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    // When request completes
    if (resultCode == RESULT_OK) {
        // Check the request code matches the one we send in the login request
        if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
            MobileServiceActivityResult result = mClient.onActivityResult(data);
            if (result.isLoggedIn()) {
                // login succeeded
                createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                createTable();
            } else {
                // login failed, check the error message
                String errorMessage = result.getErrorMessage();
                createAndShowDialog(errorMessage, "Error");
            }
        }
    }
}
```

O `GOOGLE_LOGIN_REQUEST_CODE` definido na sua principal Atividade é utilizado para o método `login()` e dentro do método `onActivityResult()`.  Pode escolher qualquer número único, desde que o mesmo número seja utilizado dentro do método `login()` e do método `onActivityResult()`.  Se abstrair o código do cliente num adaptador de serviço (como mostrado anteriormente), deve chamar os métodos adequados no adaptador de serviço.

Também precisa configurar o projeto para personalizados.  Primeiro especifique um URL redireccional.  Adicione o seguinte corte a `AndroidManifest.xml`:

```xml
<activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback"/>
    </intent-filter>
</activity>
```

Adicione o **redirecionamento UriScheme** ao ficheiro `build.gradle` para a sua aplicação:

```gradle
android {
    buildTypes {
        release {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
        debug {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
    }
}
```

Por fim, adicione `com.android.support:customtabs:28.0.0` à lista de dependências no ficheiro `build.gradle`:

```gradle
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.google.code.gson:gson:2.3'
    implementation 'com.google.guava:guava:18.0'
    implementation 'com.android.support:customtabs:28.0.0'
    implementation 'com.squareup.okhttp:okhttp:2.5.0'
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@jar'
}
```

Obtenha o ID do utilizador de sessão a partir de um **MobileServiceUser** utilizando o método **getUserId.** Para um exemplo de como usar o Futures para chamar as APIs de login assíncronos, consulte [Começar com a autenticação].

> [!WARNING]
> O sistema de URL mencionado é sensível a casos.  Certifique-se de que todas as ocorrências de `{url_scheme_of_you_app}` caso de correspondência.

### <a name="caching"></a>Fichas de autenticação cache

Os tokens de autenticação de cache requer que guarde o ID do utilizador e o símbolo de autenticação localmente no dispositivo. Da próxima vez que a aplicação começar, verifique a cache, e se estes valores estiverem presentes, pode ignorar o procedimento de login e reidratar o cliente com estes dados. No entanto, estes dados são sensíveis e devem ser armazenados encriptados por segurança no caso de o telefone ser roubado.  Pode ver um exemplo completo de como cache autenticação tokens na [secção de fichas][7]de autenticação Cache .

Quando tenta usar um símbolo expirado, recebe uma resposta *não autorizada 401.* Pode lidar com erros de autenticação utilizando filtros.  Filtros intercetam pedidos para o backend do Serviço de Aplicações. O código do filtro testa a resposta para um 401, aciona o processo de início de sessão e, em seguida, retoma o pedido que gerou o 401.

### <a name="refresh"></a>Use Refresh Tokens

O símbolo devolvido pela Autenticação e Autorização do Serviço de Aplicações Azure tem um tempo de vida definido de uma hora.  Após este período, deve reautenticar o utilizador.  Se estiver a usar um símbolo de longa duração que recebeu através da autenticação de fluxo de clientes, então pode reautenticar com autenticação e Autorização do Serviço de Aplicações Azure utilizando o mesmo símbolo.  Outro token azure App Service é gerado com uma nova vida.

Também pode registar o fornecedor para utilizar Tokens Refresh.  Um Token Refresh nem sempre está disponível.  É necessária uma configuração adicional:

* Para **o Azure Ative Directory,** configure um segredo de cliente para a App de Diretório Ativo Azure.  Especifique o segredo do cliente no Serviço de Aplicações Azure ao configurar a autenticação de diretório ativo azure.  Ao chamar `.login()`, passe `response_type=code id_token` como parâmetro:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Para o **Google,** passe o `access_type=offline` como parâmetro:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Para **a Conta Microsoft,** selecione o âmbito `wl.offline_access`.

Para refrescar um símbolo, chame `.refreshUser()`:

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

Como uma boa prática, crie um filtro que detete uma resposta 401 do servidor e tente refrescar o token do utilizador.

## <a name="log-in-with-client-flow-authentication"></a>Iniciar sessão com autenticação de fluxo de cliente

O processo geral de sessão de login com autenticação de fluxo de cliente é o seguinte:

* Configure a autenticação e a autorização do serviço de aplicações Azure, uma vez que iria autenticar o fluxo do servidor.
* Integrar o fornecedor de autenticação SDK para autenticação para produzir um sinal de acesso.
* Chame o método `.login()` da seguinte forma (`result` deve ser um `AuthenticationResult`):

    ```java
    JSONObject payload = new JSONObject();
    payload.put("access_token", result.getAccessToken());
    ListenableFuture<MobileServiceUser> mLogin = mClient.login("{provider}", payload.toString());
    Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
        @Override
        public void onFailure(Throwable exc) {
            exc.printStackTrace();
        }
        @Override
        public void onSuccess(MobileServiceUser user) {
            Log.d(TAG, "Login Complete");
        }
    });
    ```

Consulte o exemplo completo do código na secção seguinte.

Substitua o método `onSuccess()` por qualquer código que deseje utilizar num login bem sucedido.  O string `{provider}` é um fornecedor válido: **aad** (Azure Ative Directory), **facebook**, **google**, **microsoftaccount**ou **twitter**.  Se implementou a autenticação personalizada, também pode utilizar a etiqueta de autenticação personalizada.

### <a name="adal"></a>Autenticar utilizadores com a Biblioteca de Autenticação do Diretório Ativo (ADAL)

Pode utilizar a Biblioteca de Autenticação de Diretórios Ativos (ADAL) para inscrever os utilizadores na sua aplicação utilizando o Diretório Ativo do Azure. A utilização de um login de fluxo do cliente é muitas vezes preferível a usar os métodos `loginAsync()`, uma vez que proporciona uma sensação DE UX mais nativa e permite uma personalização adicional.

1. Configure o seu backend de aplicação móvel para iniciar sessão aAD seguindo o Serviço de Aplicações para tutorial de [login de Diretório Ativo.][22] Certifique-se de completar o passo opcional de registar uma aplicação de cliente nativo.
2. Instale o ADAL modificando o seu ficheiro build.gradle para incluir as seguintes definições:

    ```gradle
    repositories {
        mavenCentral()
        flatDir {
            dirs 'libs'
        }
        maven {
            url "YourLocalMavenRepoPath\\.m2\\repository"
        }
    }
    packagingOptions {
        exclude 'META-INF/MSFTSIG.RSA'
        exclude 'META-INF/MSFTSIG.SF'
    }
    dependencies {
        implementation fileTree(dir: 'libs', include: ['*.jar'])
        implementation('com.microsoft.aad:adal:1.16.1') {
            exclude group: 'com.android.support'
        } // Recent version is 1.16.1
        implementation 'com.android.support:support-v4:28.0.0'
    }
    ```

3. Adicione o seguinte código à sua aplicação, fazendo as seguintes substituições:

    * Substitua o **INSERT-AUTHORITY-HERE** pelo nome do inquilino em que aprovisionou a sua candidatura. O formato deve ser https://login.microsoftonline.com/contoso.onmicrosoft.com.
    * Substitua o **INSERT-RESOURCE-ID-AQUI** pelo ID do cliente para o seu backend de aplicação móvel. Pode obter o ID do cliente a partir do separador **Advanced** em Definições de **Diretório Ativo Azure** no portal.
    * Substitua o **INSERT-CLIENT-ID-HERE** pelo ID do cliente copiado da aplicação do cliente nativo.
    * Substitua **o INSERT-REDIRECT-URI-HERE** pelo ponto final do seu site */.auth/login/done,* utilizando o esquema HTTPS. Este valor deve ser semelhante ao *https://contoso.azurewebsites.net/.auth/login/done* .

```java
private AuthenticationContext mContext;

private void authenticate() {
    String authority = "INSERT-AUTHORITY-HERE";
    String resourceId = "INSERT-RESOURCE-ID-HERE";
    String clientId = "INSERT-CLIENT-ID-HERE";
    String redirectUri = "INSERT-REDIRECT-URI-HERE";
    try {
        mContext = new AuthenticationContext(this, authority, true);
        mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
    } catch (Exception exc) {
        exc.printStackTrace();
    }
}

private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
    @Override
    public void onError(Exception exc) {
        if (exc instanceof AuthenticationException) {
            Log.d(TAG, "Cancelled");
        } else {
            Log.d(TAG, "Authentication error:" + exc.getMessage());
        }
    }

    @Override
    public void onSuccess(AuthenticationResult result) {
        if (result == null || result.getAccessToken() == null
                || result.getAccessToken().isEmpty()) {
            Log.d(TAG, "Token is empty");
        } else {
            try {
                JSONObject payload = new JSONObject();
                payload.put("access_token", result.getAccessToken());
                ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        exc.printStackTrace();
                    }
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        Log.d(TAG, "Login Complete");
                    }
                });
            }
            catch (Exception exc){
                Log.d(TAG, "Authentication error:" + exc.getMessage());
            }
        }
    }
};

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (mContext != null) {
        mContext.onActivityResult(requestCode, resultCode, data);
    }
}
```

## <a name="filters"></a>Ajuste a Comunicação Cliente-Servidor

A ligação cliente é normalmente uma ligação HTTP básica utilizando a biblioteca HTTP subjacente fornecida com o Android SDK.  Há várias razões pelas quais gostaria de mudar isso:

* Deseja utilizar uma biblioteca http alternativa para ajustar os intervalos.
* Você deseja fornecer uma barra de progresso.
* Deseja adicionar um cabeçalho personalizado para suportar a funcionalidade de gestão da API.
* Deseja intercetar uma resposta falhada para que possa implementar a reautenticação.
* Deseja registar pedidos de backend para um serviço de análise.

### <a name="using-an-alternate-http-library"></a>Usando uma biblioteca http alternativa

Ligue para o método `.setAndroidHttpClientFactory()` imediatamente após criar a referência do seu cliente.  Por exemplo, para definir o tempo de ligação para 60 segundos (em vez dos 10 segundos predefinidos):

```java
mClient = new MobileServiceClient("https://myappname.azurewebsites.net");
mClient.setAndroidHttpClientFactory(new OkHttpClientFactory() {
    @Override
    public OkHttpClient createOkHttpClient() {
        OkHttpClient client = new OkHttpClient();
        client.setReadTimeout(60, TimeUnit.SECONDS);
        client.setWriteTimeout(60, TimeUnit.SECONDS);
        return client;
    }
});
```

### <a name="implement-a-progress-filter"></a>Implementar um filtro de progresso

Pode implementar uma interceção de cada pedido implementando um `ServiceFilter`.  Por exemplo, as seguintes atualizações uma barra de progresso pré-criada:

```java
private class ProgressFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        final SettableFuture<ServiceFilterResponse> resultFuture = SettableFuture.create();
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.VISIBLE);
            }
        });

        ListenableFuture<ServiceFilterResponse> future = next.onNext(request);
        Futures.addCallback(future, new FutureCallback<ServiceFilterResponse>() {
            @Override
            public void onFailure(Throwable e) {
                resultFuture.setException(e);
            }
            @Override
            public void onSuccess(ServiceFilterResponse response) {
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        if (mProgressBar != null)
                            mProgressBar.setVisibility(ProgressBar.GONE);
                    }
                });
                resultFuture.set(response);
            }
        });
        return resultFuture;
    }
}
```

Pode anexar este filtro ao cliente da seguinte forma:

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>Personalizar cabeçalhos de pedido

Utilize o seguinte `ServiceFilter` e fixe o filtro da mesma forma que o `ProgressFilter`:

```java
private class CustomHeaderFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                request.addHeader("X-APIM-Router", "mobileBackend");
            }
        });
        SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
        try {
            ServiceFilterResponse response = next.onNext(request).get();
            result.set(response);
        } catch (Exception exc) {
            result.setException(exc);
        }
    }
}
```

### <a name="conversions"></a>Configurar a serialização automática

Pode especificar uma estratégia de conversão que se aplica a todas as colunas utilizando a API do [gson.][3] A biblioteca de clientes Android usa [gson][3] nos bastidores para serializar objetos Java para dados JSON antes de os dados forem enviados para o Azure App Service.  O código seguinte utiliza o método **setFieldNamingStrategy()** para definir a estratégia. Este exemplo eliminará o personagem inicial (um "m" e, em seguida, minúscula o próximo personagem, para cada nome de campo. Por exemplo, transformaria "mId" em "id".  Implementar uma estratégia de conversão para reduzir a necessidade de anotações `SerializedName()` na maioria dos campos.

```java
FieldNamingStrategy namingStrategy = new FieldNamingStrategy() {
    public String translateName(File field) {
        String name = field.getName();
        return Character.toLowerCase(name.charAt(1)) + name.substring(2);
    }
}

client.setGsonBuilder(
    MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(namingStrategy)
);
```

Este código deve ser executado antes de criar uma referência móvel de cliente utilizando o **MobileServiceClient**.

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: https://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
[Começar com a autenticação]: app-service-mobile-android-get-started-users.md
[1]: https://static.javadoc.io/com.google.code.gson/gson/2.8.5/com/google/gson/JsonObject.html
[2]: https://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: https://www.javadoc.io/doc/com.google.code.gson/gson/2.8.5
[4]: https://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: https://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: https://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: https://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: https://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[17]: https://developer.android.com/reference/java/util/UUID.html
[18]: https://github.com/google/guava/wiki/ListenableFutureExplained
[19]: https://www.odata.org/documentation/odata-version-3-0/
[20]: https://hashtagfail.com/post/46493261719/mobile-services-android-querying
[21]: https://github.com/Azure-Samples/azure-mobile-apps-android-quickstart
[22]: ../app-service/configure-authentication-provider-aad.md
[Future]: https://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: https://developer.android.com/reference/android/os/AsyncTask.html
