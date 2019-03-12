---
title: Como utilizar o SDK de aplicações móveis do Azure para Android | Documentos da Microsoft
description: Como utilizar o SDK de aplicações móveis do Azure para Android
services: app-service\mobile
documentationcenter: android
author: conceptdev
manager: crdun
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 03/07/2019
ms.author: crdun
ms.openlocfilehash: 45b5ac0c9b3535e5cc5efdc6827d694b41e0b8dd
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57732104"
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Como utilizar o SDK de aplicações móveis do Azure para Android

Este guia mostra-lhe como utilizar o Android SDK do cliente para aplicações móveis para implementar cenários comuns, tais como:

* Consultar dados (inserir, atualizar e eliminar).
* Autenticação.
* Tratamento de erros.
* Personalizar o cliente.

Este guia enfoca o SDK Android do lado do cliente.  Para saber mais sobre os SDKs do lado do servidor para aplicações móveis, consulte [trabalhar com back-end de .NET SDK] [ 10] ou [como utilizar o back-end de node. js SDK][11].

## <a name="reference-documentation"></a>Documentação de referência

Pode encontrar os [referência da API de Javadocs] [ 12] para a biblioteca de cliente Android no GitHub.

## <a name="supported-platforms"></a>Plataformas suportadas

O SDK de aplicações móveis do Azure para Android suporta níveis de API 19 por meio de 24 (KitKat por meio de Nougat) para fatores forma de telemóveis e tablets.  A autenticação, em particular, utiliza uma abordagem comum para a estrutura de web para reunir as credenciais.  Autenticação de servidor fluxo não funciona com dispositivos de fator de formulário pequeno como watches.

## <a name="setup-and-prerequisites"></a>Configuração e pré-requisitos

Concluir o [início rápido de aplicações móveis](app-service-mobile-android-get-started.md) tutorial.  Esta tarefa garante que todos os pré-requisitos para o desenvolvimento de aplicações móveis do Azure foram cumpridos.  O guia de introdução ajuda-o a configurar a sua conta e criar seu primeiro back-end de aplicação móvel.

Se decidir não concluir o tutorial de início rápido, conclua as seguintes tarefas:

* [criar um back-end de aplicação móvel] [ 13] para utilizar com a sua aplicação Android.
* No Android Studio, [atualização do Gradle criar arquivos](#gradle-build).
* [Ativar a permissão de internet](#enable-internet).

### <a name="gradle-build"></a>Atualizar o ficheiro de compilação do Gradle

Altere a ambos **gradle** ficheiros:

1. Adicione este código para o *Project* nível **gradle** ficheiro:

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

2. Adicione este código para o *aplicação de módulo* nível **gradle** dentro do ficheiro a *dependências* etiqueta:

    ```gradle
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    Atualmente, a versão mais recente é 3.4.0. As versões suportadas são listadas [na gaveta][14].

### <a name="enable-internet"></a>Ativar a permissão de internet

Para aceder ao Azure, a aplicação tem de ter a permissão de INTERNET ativada. Se ainda não estiver ativado, adicione a seguinte linha de código para seu **androidmanifest. XML** ficheiro:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Criar uma ligação de cliente

Aplicações móveis do Azure fornece quatro funções da aplicação móvel:

* Acesso a dados e Sincronização Offline com um serviço de aplicações móveis do Azure.
* Chame APIs personalizadas escritas com o SDK de servidor de aplicações do Azure Mobile.
* Autenticação com o serviço de aplicações do Azure de autenticação e autorização.
* Registo de notificação com os Hubs de notificação de push.

Cada uma dessas funções primeiro exige que crie um `MobileServiceClient` objeto.  Apenas um `MobileServiceClient` objeto deve ser criado dentro de seu cliente móvel (ou seja, ele deve ser um padrão de Singleton).  Para criar um `MobileServiceClient` objeto:

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

O `<MobileAppUrl>` é uma cadeia de caracteres ou um objeto de URL que aponta para o seu back-end móvel.  Se estiver a utilizar o serviço de aplicações do Azure para alojar o back-end móvel, em seguida, certifique-se de que utiliza a segura `https://` versão do URL.

O cliente também precisa de acesso para a atividade ou contexto - o `this` parâmetro no exemplo.  A construção de MobileServiceClient deve acontecer dentro do `onCreate()` método da atividade referenciada no `AndroidManifest.xml` ficheiro.

Como melhor prática, deve abstrair a comunicação de servidor em sua própria classe (padrão de singleton).  Neste caso, deve passar a atividade dentro do construtor para configurar adequadamente o serviço.  Por exemplo:

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

Pode chamar `AzureServiceAdapter.Initialize(this);` no `onCreate()` método de sua atividade principal.  Outros métodos de que precisam de acesso para a utilização do cliente `AzureServiceAdapter.getInstance();` para obter uma referência para o adaptador de serviço.

## <a name="data-operations"></a>Operações de Dados

É o núcleo do SDK de aplicações móveis do Azure fornecer acesso aos dados armazenados no SQL Azure no back-end da aplicação móvel.  Pode acessar esses dados usando as classes com rigidez de tipos (preferidas) ou sem tipos de consultas (não recomendadas).  A maior parte desta secção lida com o uso de classes com rigidez de tipos.

### <a name="define-client-data-classes"></a>Definir as classes de dados do cliente

Para acessar dados de tabelas do SQL Azure, defina as classes de dados de cliente que correspondem para as tabelas no back-end da aplicação móvel. Exemplos neste tópico partem do princípio de uma tabela chamada **MyDataTable**, que tem as seguintes colunas:

* ID
* texto
* Concluir

O objeto de lado do cliente com tipos correspondente reside num arquivo chamado **MyDataTable.java**:

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Adicione os métodos getter e setter para cada campo que adicionar.  Se a tabela do SQL Azure contiver mais colunas, adicionaria os campos correspondentes a essa classe.  Por exemplo, se o DTO (objeto de transferência de dados) tinha uma coluna de prioridade de número inteiro, em seguida, poderá adicionar este campo, juntamente com seus métodos getter e setter:

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

Para saber como criar tabelas adicionais no seu back-end de aplicações móveis, consulte [como: Definir um table controller] [ 15] (back-end do .NET) ou [definir tabelas com um esquema dinâmico] [ 16] (back-end do node. js).

Uma tabela de back-end de aplicações móveis do Azure define cinco campos especiais, quatro delas são disponibilizados aos clientes:

* `String id`: O ID exclusivo global para o registo.  Como melhor prática, verifique o id a representação de cadeia de caracteres de um [UUID] [ 17] objeto.
* `DateTimeOffset updatedAt`: A data/hora da última atualização.  O campo de updatedAt é definido pelo servidor e nunca deve ser definido pelo seu código de cliente.
* `DateTimeOffset createdAt`: A data/hora que o objeto foi criado.  O campo de createdAt é definido pelo servidor e nunca deve ser definido pelo seu código de cliente.
* `byte[] version`: Normalmente, é representado como uma cadeia de caracteres, a versão também é definida pelo servidor.
* `boolean deleted`: Indica que o registo foi eliminado, mas não removido ainda.  Não utilize `deleted` como uma propriedade na sua classe.

O campo `id` é obrigatório.  O `updatedAt` campo e `version` campo são utilizados para sincronização offline (para a resolução de sincronização e o conflito incremental, respectivamente).  O `createdAt` campo é um campo de referência e não é utilizado pelo cliente.  Os nomes são "entre-the-wire" nomes das propriedades e não são ajustável.  No entanto, pode criar um mapeamento entre o objeto e os nomes de "entre-the-wire" com o [gson] [ 3] biblioteca.  Por exemplo:

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

Para aceder a uma tabela, primeiro crie uma [MobileServiceTable] [ 8] objeto chamando o **getTable** método no [MobileServiceClient] [9].  Este método tem duas sobrecargas:

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

No código a seguir **mClient** é uma referência para seu objeto MobileServiceClient.  A primeira sobrecarga é utilizada em que o nome da classe e o nome da tabela são os mesmos e é aquele usado no início rápido:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

A segunda sobrecarga é utilizada quando o nome da tabela é diferente do nome de classe: o primeiro parâmetro é o nome da tabela.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query"></a>Consultar uma tabela de back-end

Em primeiro lugar, obtenha uma referência de tabela.  Em seguida, execute uma consulta na referência de tabela.  Uma consulta é qualquer combinação de:

* R `.where()` [cláusula de filtro](#filtering).
* Uma `.orderBy()` [ordenação cláusula](#sorting).
* R `.select()` [cláusula de seleção do campo](#selection).
* R `.skip()` e `.top()` para [paginada resultados](#paging).

As cláusulas devem ser apresentadas na ordem anterior.

### <a name="filter"></a> Filtragem de resultados

O formato geral de uma consulta é:

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

O exemplo anterior devolve todos os resultados (até o tamanho de página máximo definido pelo servidor).  O `.execute()` método executa a consulta no back-end.  A consulta é convertida para um [OData v3] [ 19] consulta antes da transmissão para o back-end de aplicações móveis.  Após a receção, o back-end de aplicações móveis converte a consulta numa instrução de SQL antes de executá-lo na instância do SQL Azure.  Uma vez que a atividade de rede demora algum tempo, o `.execute()` método devolve um [ `ListenableFuture<E>` ] [ 18].

### <a name="filtering"></a>Filtrar dados devolvidos

A execução da consulta seguinte devolve todos os itens do **ToDoItem** tabela onde **completa** é igual a **false**.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** é a referência à tabela de serviço móvel que criamos anteriormente.

Definir um filtro através da **onde** chamada de método na referência de tabela. O **em que** método é seguido por uma **campo** método seguido de um método que especifica o predicado de lógico. Métodos de predicado possíveis incluem **eq** (igual a), **ne** (não igual) **gt** (maior que) **ge** (maior ou igual a), **lt** (inferior), **le** (menor ou igual a). Esses métodos permitem-lhe comparar o número e a cadeia de campos a valores específicos.

Pode filtrar as datas. Os métodos seguintes permitem-lhe comparar o campo de data todo ou partes da data: **ano**, **mês**, **dia**, **hora**,  **minuto**, e **segundo**. O exemplo seguinte adiciona um filtro para itens cujo *data de vencimento* é igual a 2013.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

Os seguintes métodos de suportam filtros complexos em campos de cadeia de caracteres: **startsWith**, **endsWith**, **concat**, **subcadeia**, **indexOf**, **substitua**, **toLower**, **toUpper**, **trim**, e **comprimento** . Os seguintes filtros de exemplo para a tabela linhas em que o *texto* coluna começa com "PRI0."

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

Os seguintes métodos de operador são suportados em campos de números: **adicione**, **sub**, **mul**, **div**, **mod**, **piso**, **teto**, e **arredondar**. Os seguintes filtros de exemplo para a tabela linhas em que o **duração** é um número par.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

Pode combinar predicados com esses métodos lógicos: **e**, **ou** e **não**. O exemplo seguinte combina dois exemplos anteriores.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

Agrupar e aninhar operadores lógicos:

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

Para obter mais exemplos de filtragem e uma discussão, consulte [explorando a riqueza do modelo de consulta de cliente Android][20].

### <a name="sorting"></a>Ordenar dados devolvido

O código a seguir devolve todos os itens de uma tabela **ToDoItems** ordenação ascendente pela *texto* campo. *mToDoTable* é a referência à tabela de back-end que criou anteriormente:

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

O primeiro parâmetro do **orderBy** método é uma cadeia igual ao nome do campo no qual ordenar. O segundo parâmetro utiliza a **QueryOrder** enumeração para especificar se pretende ordenar em ascendente ou descendente.  Se estiver a filtrar utilizando o ***em que*** método, o ***onde*** método deve ser chamado antes do ***orderBy*** método.

### <a name="selection"></a>Selecionar colunas específicas

O código a seguir ilustra como devolver todos os itens de uma tabela **ToDoItems**, mas apenas apresenta os **completa** e **texto** campos. **mToDoTable** é a referência à tabela de back-end que criamos anteriormente.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

Os parâmetros para a função selecione são os nomes de cadeia de caracteres de colunas da tabela que queira retornar.  O **selecionar** método tem de cumprir métodos como **onde** e **orderBy**. Ele pode ser seguido por métodos de paginação, como **ignore** e **superior**.

### <a name="paging"></a>Devolve os dados nas páginas

Os dados são **sempre** devolvido nas páginas.  O número máximo de registos devolvidos é definido pelo servidor.  Se o cliente solicitar mais registos, o servidor devolve o número máximo de registos.  Por predefinição, o tamanho máximo de página no servidor é 50 registos.

O primeiro exemplo mostra como selecionar os itens de cinco principais de uma tabela. A consulta devolve os itens de uma tabela **ToDoItems**. **mToDoTable** é a referência à tabela de back-end que criou anteriormente:

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

Eis uma consulta que ignora os primeiros cinco itens e, em seguida, devolve os próximos cinco:

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Se quiser obter todos os registos numa tabela, implemente o código para iterar sobre todas as páginas:

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

Um pedido para todos os registos ao utilizar este método cria um mínimo de duas solicitações para o back-end de aplicações móveis.

> [!TIP]
> Escolher o tamanho de página correta é um equilíbrio entre a utilização de memória enquanto o pedido está a acontecer, a utilização de largura de banda e a atraso em receber os dados completamente.  A predefinição (50 registos) é adequada para todos os dispositivos.  Se trabalha exclusivamente em dispositivos de memória maior, aumente até 500.  Descobrimos que a aumentar o tamanho de página além dos 500 regista resultados em atrasos inaceitáveis e problemas de memória de grandes dimensões.

### <a name="chaining"></a>Como: Concatenar os métodos de consulta

Os métodos utilizados na consulta de tabelas de back-end podem ser concatenados. Encadeamento de métodos de consulta permite-lhe selecionar colunas específicas de linhas filtradas que são classificadas e bloco paginadas. Pode criar filtros complexos de lógicos.  Cada método de consulta retorna um objeto de consulta. Para terminar a série de métodos e, na verdade, execute a consulta, chame o **executar** método. Por exemplo:

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

Os métodos em cadeia de consulta tem de ser ordenados como segue:

1. Filtragem (**onde**) métodos.
2. Classificação (**orderBy**) métodos.
3. Seleção (**selecione**) métodos.
4. paginação (**ignore** e **superior**) métodos.

## <a name="binding"></a>Ligar dados à interface do usuário

Vinculação de dados envolve três componentes:

* A origem de dados
* O esquema do ecrã
* O adaptador que une os dois.

No nosso código de exemplo, vamos retornar os dados da tabela de SQL Azure de aplicações móveis **ToDoItem** numa matriz. Esta atividade é um padrão comum para aplicações de dados.  Consultas de base de dados, muitas vezes, retornam uma coleção de linhas que o cliente obtém uma lista ou uma matriz. Neste exemplo, a matriz é a origem de dados.  O código especifica um esquema de ecrã que define a exibição dos dados que é apresentado no dispositivo.  Os dois são ligados em conjunto com um adaptador, o que, neste código, é uma extensão do **ArrayAdapter&lt;ToDoItem&gt;**  classe.

#### <a name="layout"></a>Definir o Layout

O esquema é definido por vários fragmentos de código XML. Tendo em conta um layout existente, o código a seguir representa o **ListView** que queremos preencher com os nossos dados de servidor.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

No código anterior, o *listitem* atributo especifica o id do layout para uma linha individual na lista. Esse código especifica uma caixa de verificação e o respetivo texto associado e será instanciado uma vez para cada item na lista. Este esquema não apresenta o **id** campo e um esquema mais complexo de especificar campos adicionais na exibição. Esse código está no **row_list_to_do.xml** ficheiro.

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

#### <a name="adapter"></a>Definir o adaptador
Uma vez que a origem de dados da nossa exibição é uma matriz de **ToDoItem**, podemos subclasse nosso adaptador de um **ArrayAdapter&lt;ToDoItem&gt;**  classe. Este subclasse produz uma exibição para cada **ToDoItem** utilizando o **row_list_to_do** layout.  No nosso código, definimos a seguinte classe que é uma extensão do **ArrayAdapter&lt;i&gt;**  classe:

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

Substituir os adaptadores **getView** método. Por exemplo:

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

Criamos uma instância dessa classe na nossa atividade da seguinte forma:

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

O segundo parâmetro para o construtor de ToDoItemAdapter é uma referência ao layout. Podemos agora pode criar uma instância do **ListView** e atribua o adaptador para o **ListView**.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-adapter"></a>Utilizar o adaptador para o enlace para a interface do Usuário

Agora está pronto para usar a vinculação de dados. O código a seguir mostra como obter itens na tabela e preenche o adaptador de local com os itens retornados.

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

Chamar o adaptador sempre que modificar a **ToDoItem** tabela. Uma vez que as modificações são efetuadas numa base de Registro por registro, lidar com uma única linha em vez de uma coleção. Ao inserir um item, chamar o **adicione** método no adaptador; quando a eliminação, chame o **remover** método.

Pode encontrar um exemplo completo no [Android projeto de início rápido][21].

## <a name="inserting"></a>Inserir dados no back-end

Criar uma instância dos *ToDoItem* de classe e definir as respetivas propriedades.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Em seguida, utilize **INSERT** para inserção de um objeto:

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

As correspondências de entidades retornadas os dados inseridos na tabela de back-end, incluído o ID e quaisquer outros valores (como o `createdAt`, `updatedAt`, e `version` campos) conjunto de back-end.

As tabelas de aplicações móveis requerem uma coluna de chave primária com o nome **id**. Esta coluna tem de ser uma cadeia de caracteres. O valor predefinido da coluna ID é um GUID.  Pode fornecer outros valores exclusivos, como endereços de e-mail ou nomes de utilizador. Quando um valor de ID de cadeia não é oferecido para um registo inserido, o back-end gera um novo GUID.

Valores de cadeia de ID fornecem as seguintes vantagens:

* IDs podem ser gerados sem fazer uma ida e volta ao banco de dados.
* Os registos são mais fáceis de intercalação de tabelas diferentes ou bases de dados.
* Valores de ID de integram-se melhor com a lógica de um aplicativo.

Os valores de ID de cadeia de caracteres são **obrigatório** para suporte de sincronização offline.  Não é possível alterar um Id de uma vez que ele é armazenado na base de dados back-end.

## <a name="updating"></a>Atualizar os dados de uma aplicação móvel

Para atualizar os dados numa tabela, passar o objeto novo para o **Update ()** método.

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

Neste exemplo, *item* é uma referência a uma linha na *ToDoItem* tabela, que tenha tido algumas alterações efetuadas ao mesmo.  A linha com o mesmo **id** é atualizado.

## <a name="deleting"></a>Eliminar dados numa aplicação móvel

O código seguinte mostra como eliminar dados de uma tabela, especificando o objeto de dados.

```java
mToDoTable
    .delete(item);
```

Também pode eliminar um item, especificando o **id** campo da linha a eliminar.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="lookup"></a>Procurar um item específico por Id

Procurar um item com um específico **id** campo com o **lookUp()** método:

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="untyped"></a>Como: Trabalhar com dados sem tipo

O modelo de programação sem tipo dá-lhe exatamente o controle sobre a serialização do JSON.  Existem alguns cenários comuns em que poderá pretender utilizar um modelo de programação sem tipo. Por exemplo, se a sua tabela de back-end contém o número de colunas e só precisa de fazer referência a um subconjunto das colunas.  O modelo de tipo exige que defina todas as colunas definidas no back-end de aplicações móveis na sua classe de dados.  A maioria das chamadas de API para aceder aos dados são semelhantes às chamadas de programação de tipos. A principal diferença é que no modelo sem tipo invocar métodos no **MobileServiceJsonTable** objeto, em vez do **MobileServiceTable** objeto.

### <a name="json_instance"></a>Criar uma instância de uma tabela sem tipo

Assim como o modelo de tipo, comece por obter uma referência de tabela, mas neste caso é um **MobileServicesJsonTable** objeto. Obter a referência ao chamar o **getTable** método numa instância do cliente:

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

Depois de criar uma instância do **MobileServiceJsonTable**, é praticamente a mesma API disponível como com o modelo de programação de tipos. Em alguns casos, os métodos assumem um parâmetro sem tipo, em vez de um parâmetro de tipo.

### <a name="json_insert"></a>Inserir uma tabela sem tipo
O código a seguir mostra como fazer uma inserção. A primeira etapa é criar um [JsonObject][1], que faz parte dos [gson] [ 3] biblioteca.

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Em seguida, utilize **INSERT** para inserir o objeto sem tipo na tabela.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Se precisar de obter o ID do objeto inserido, utilize o **getAsJsonPrimitive()** método.

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="json_delete"></a>Eliminar a partir de uma tabela sem tipo
O código seguinte mostra como eliminar uma instância, neste caso, a mesma instância de um **JsonObject** que foi criada no antes *inserir* exemplo. O código é o mesmo, tal como acontece com o caso de tipos, mas o método tem uma assinatura diferente, porque referencia uma **JsonObject**.

```java
mToDoTable
    .delete(insertedItem);
```

Também pode eliminar uma instância diretamente, utilizando o respetivo ID:

```java
mToDoTable.delete(ID);
```

### <a name="json_get"></a>Devolver todas as linhas a partir de uma tabela sem tipo
O código a seguir mostra como recuperar uma tabela inteira. Uma vez que estiver a utilizar uma tabela de JSON, pode obter apenas algumas das colunas da tabela seletivamente.

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

O mesmo conjunto de filtragem, filtragem e métodos que estão disponíveis para o modelo de tipo de paginação estão disponíveis para o modelo sem tipo.

## <a name="offline-sync"></a>Implementar a Sincronização Offline

O SDK de cliente de aplicações do Azure Mobile também implementa a sincronização offline de dados ao utilizar uma base de dados do SQLite para armazenar uma cópia dos dados de servidor localmente.  As operações executadas numa tabela offline não necessitam de conectividade móvel para trabalhar.  Sincronização offline auxilia na resiliência e desempenho às custas de uma lógica mais complexa para resolução de conflitos.  O Azure Mobile Apps cliente SDK implementa as seguintes funcionalidades:

* Sincronização incremental: Apenas são transferidos registros novos e atualizados, salvar o consumo de largura de banda e de memória.
* Simultaneidade otimista: Operações devem ter êxito.  Resolução de conflito for adiada até que as atualizações são executadas no servidor.
* Resolução de conflitos: O SDK Deteta quando uma alteração em conflito foi efetuada no servidor e forneça ganchos para alertar o utilizador.
* Eliminação de forma recuperável: Registos eliminados são marcadas como eliminados, permitindo que os outros dispositivos atualizar a respetiva cache offline.

### <a name="initialize-offline-sync"></a>Inicializar a Sincronização Offline

Cada tabela offline tem de ser definida no cache offline antes de utilização.  Normalmente, a definição da tabela é feita imediatamente após a criação do cliente:

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

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>Obter uma referência para a tabela de Cache Offline

Para uma tabela de online, utilize `.getTable()`.  Para uma tabela offline, utilize `.getSyncTable()`:

```java
MobileServiceSyncTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

Todos os métodos disponíveis para tabelas online (incluindo a filtragem, classificação, paginação, inserção de dados, a atualização de dados e a eliminação de dados) funcionam igualmente bem em tabelas online e offline.

### <a name="synchronize-the-local-offline-cache"></a>Sincronizar a Cache Offline Local

A sincronização está no âmbito do seu aplicativo.  Este é um método de sincronização de exemplo:

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

Se não for fornecido um nome de consulta para o `.pull(query, queryname)` método, em seguida, sincronização incremental é utilizado para devolver apenas registos que foram criados ou alterados desde o último com êxito concluir a solicitação.

### <a name="handle-conflicts-during-offline-synchronization"></a>Lidar com conflitos durante a Sincronização Offline

Se um conflito acontece durante uma `.push()` operação, um `MobileServiceConflictException` é lançada.   O item de servidor emitido está incorporado a exceção e podem ser obtido por `.getItem()` na exceção.  Ajuste o push chamando os seguintes itens no objeto MobileServiceSyncContext:

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

Depois de todos os conflitos são marcados como desejar, chamar `.push()` novamente para resolver todos os conflitos.

## <a name="custom-api"></a>Chamar uma API personalizada

Uma API personalizada permite-lhe definir os pontos finais personalizados que expõem funcionalidades do servidor que não mapeiam para uma inserção, atualizar, eliminar ou operação de leitura. Ao utilizar uma API personalizada, pode ter mais controle sobre as mensagens, inclusive leitura e definir cabeçalhos de mensagem HTTP e definir um formato de corpo de mensagem diferente do JSON.

De um cliente do Android, chama o **invokeApi** método para chamar o ponto final da API personalizado. O exemplo seguinte mostra como chamar um ponto final de API com o nome **completeAll**, que retorna uma classe de coleção chamada **MarkAllResult**.

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

O **invokeApi** método é chamado no cliente, que envia um pedido POST para a nova API personalizada. O resultado devolvido pela API personalizada é apresentado numa caixa de diálogo de mensagem, como erros. Outras versões do **invokeApi** permitem-lhe, opcionalmente, enviar um objeto no corpo do pedido, especifique o método HTTP e enviar parâmetros de consulta com o pedido. Sem tipos versões do **invokeApi** são fornecidos também.

## <a name="authentication"></a>Adicionar autenticação à sua aplicação

Tutoriais descrevem já em detalhes como adicionar esses recursos.

Serviço de aplicações suporta [autenticar os utilizadores da aplicação](app-service-mobile-android-get-started-users.md) com vários fornecedores de identidade externo: Do Facebook, Google, conta Microsoft, Twitter e o Azure Active Directory. Pode definir permissões em tabelas para restringir o acesso para operações específicas para apenas os utilizadores autenticados. Também pode utilizar a identidade de usuários autenticados para implementar regras de autorização no seu back-end.

Dois fluxos de autenticação são suportados: uma **servidor** fluxo e um **cliente** fluxo. O fluxo de servidor fornece a experiência de autenticação mais simples, como ele conta com a interface de web de fornecedores de identidade.  Não existem SDKs adicionais são necessários para implementar a autenticação de fluxo de servidor. Autenticação de fluxo de servidor não fornece uma integração profunda num dispositivo móvel e só é recomendada para uma prova de cenários de conceito.

O fluxo de cliente permite uma integração mais profunda com recursos específicos do dispositivo, como o início de sessão único, pois depende SDKs fornecidos pelo fornecedor de identidade.  Por exemplo, pode integrar o SDK do Facebook a sua aplicação móvel.  O cliente móvel troca para a aplicação do Facebook e confirma o início de sessão antes de passar para a sua aplicação móvel.

Quatro passos são necessários para ativar a autenticação na sua aplicação:

* Registe a sua aplicação para a autenticação com um fornecedor de identidade.
* Configure o seu back-end do serviço de aplicações.
* Restringir permissões de tabela para utilizadores autenticados apenas no back-end do serviço de aplicações.
* Adicione o código de autenticação à sua aplicação.

Pode definir permissões em tabelas para restringir o acesso para operações específicas para apenas os utilizadores autenticados. Também pode utilizar o SID de um utilizador autenticado para modificar pedidos.  Para obter mais informações, consulte [introdução à autenticação] e a documentação de tutoriais sobre como SDK do servidor.

### <a name="caching"></a>Autenticação: Fluxo de servidor

O código seguinte inicia um processo de início de sessão de fluxo de servidor utilizando o fornecedor do Google.  É necessária configuração adicional devido aos requisitos de segurança para o fornecedor do Google:

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

Além disso, adicione o seguinte método à classe da atividade principal:

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

O `GOOGLE_LOGIN_REQUEST_CODE` definidos em sua principal atividade é utilizada para o `login()` método e dentro do `onActivityResult()` método.  Pode escolher qualquer número exclusivo, desde que o mesmo número é utilizado dentro de `login()` método e o `onActivityResult()` método.  Se abstrair o código de cliente num adaptador de serviço (como mostrado anteriormente), deve chamar os métodos apropriados no adaptador de serviço.

Terá também de configurar o projeto para customtabs.  Primeiro, especifique um URL de redirecionamento.  Adicione o seguinte fragmento para `AndroidManifest.xml`:

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

Adicionar a **redirectUriScheme** para o `build.gradle` ficheiro para a sua aplicação:

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

Por fim, adicione `com.android.support:customtabs:28.0.0` à lista de dependências no `build.gradle` ficheiro:

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

Obtenha o ID do utilizador com sessão iniciada de um **MobileServiceUser** utilizando o **getUserId** método. Para obter um exemplo de como usá-los para chamar o APIs de início de sessão assíncrono, consulte [introdução à autenticação].

> [!WARNING]
> O esquema de URL mencionado diferencia maiúsculas de minúsculas.  Certifique-se de que todas as ocorrências de `{url_scheme_of_you_app}` diferenciar maiúsculas de minúsculas.

### <a name="caching"></a>Tokens de autenticação de cache

Colocação em cache de tokens de autenticação, tem de armazenar o ID de utilizador e o token de autenticação localmente no dispositivo. Da próxima vez que a aplicação for iniciada, verifique o cache, e se esses valores estão presentes, pode ignorar o procedimento de início de sessão e realimentar o cliente com estes dados. No entanto estes dados são confidenciais e devem ser armazenado encriptados para segurança, no caso do telefone for roubado.  Pode ver um exemplo completo de como para tokens de autenticação de cache no [secção de tokens de autenticação da Cache][7].

Quando tentar usar um token expirado, receberá um *401 não autorizado* resposta. Pode manipular erros de autenticação a utilizar os filtros.  Filtros de interceptar solicitações para o back-end do serviço de aplicações. O código de filtro testa a resposta para um erro 401, aciona o processo de início de sessão e, em seguida, retoma a solicitação que gerou a 401.

### <a name="refresh"></a>Utilizar Tokens de atualização

O token devolvido pelo autorização e autenticação de serviço de aplicações do Azure tem um tempo de vida definido de uma hora.  Após este período, tem de autenticar o utilizador.  Se estiver a utilizar um token de vida longa que tenha recebido através da autenticação de fluxo de cliente, em seguida, pode autenticar com a autenticação de serviço de aplicações do Azure e com o mesmo token de autorização.  Outro token do serviço de aplicações do Azure é gerado com uma duração de novo.

Também pode registar o fornecedor a utilizar Tokens de atualização.  Um Token de atualização não está sempre disponível.  É necessária configuração adicional:

* Para **do Azure Active Directory**, configurar um segredo do cliente para a aplicação do Azure Active Directory.  Especifica o segredo do cliente no serviço de aplicações do Azure ao configurar a autenticação do Active Directory do Azure.  Ao chamar `.login()`, passar `response_type=code id_token` como um parâmetro:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Para **Google**, passar o `access_type=offline` como um parâmetro:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Para **Microsoft Account**, selecione o `wl.offline_access` âmbito.

Para atualizar um token, chamar `.refreshUser()`:

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

Como melhor prática, crie um filtro que Deteta uma resposta 401 do servidor e tenta atualizar o token de utilizador.

## <a name="log-in-with-client-flow-authentication"></a>Iniciar sessão com a autenticação de fluxo de cliente

O processo geral para iniciar sessão com a autenticação de fluxo de cliente é o seguinte:

* Configure a autorização e autenticação de serviço de aplicações do Azure tal como faria com autenticação de fluxo de servidor.
* Integre o SDK para a autenticação para produzir um token de acesso do fornecedor de autenticação.
* Chamar o `.login()` método da seguinte forma (`result` deve ser um `AuthenticationResult`):

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

Veja o exemplo de código completo na próxima seção.

Substitua o `onSuccess()` método com qualquer código que deseja usar num início de sessão com êxito.  O `{provider}` cadeia de caracteres é um fornecedor válido: **aad** (Azure Active Directory), **facebook**, **google**, **microsoftaccount**, ou **twitter**.  Se tiver implementado a autenticação personalizada, em seguida, também pode utilizar a marca de fornecedor de autenticação personalizado.

### <a name="adal"></a>Autenticar utilizadores com o Active Directory Authentication Library (ADAL)

Pode utilizar o Active Directory Authentication Library (ADAL) para iniciar sessão de utilizadores na sua aplicação utilizando o Azure Active Directory. Com um início de sessão de fluxo de cliente, muitas vezes, é preferível a utilizar o `loginAsync()` métodos como ele fornece uma noção da experiência do Usuário mais nativa e permite a personalização adicional.

1. Configurar o seu back-end de aplicação móvel para o início de sessão do AAD ao seguir a [como configurar o serviço de aplicações para início de sessão do Active Directory] [ 22] tutorial. Certifique-se concluir o passo opcional de registar uma aplicação cliente nativa.
2. Instale a ADAL, modificando o ficheiro de gradle para incluir as seguintes definições:

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

3. Adicione o seguinte código para seu aplicativo, tornando as substituições seguintes:

    * Substitua **INSERT-autoridade-HERE** com o nome do inquilino que aprovisionou seu aplicativo. O formato deve ser https://login.microsoftonline.com/contoso.onmicrosoft.com.
    * Substitua **INSERT-RESOURCE-ID-HERE** com o ID de cliente para o back-end de aplicação móvel. Pode obter o ID de cliente do **avançadas** separador sob **definições de diretório do Azure Active Directory** no portal.
    * Substitua **INSERT-CLIENT-ID-HERE** com o ID de cliente que copiou da aplicação cliente nativa.
    * Substitua **INSERT-REDIRECIONAMENTO-URI-HERE** com o seu site */.auth/login/done* ponto de extremidade, usando o esquema HTTPS. Este valor deve ser semelhante à *https://contoso.azurewebsites.net/.auth/login/done*.

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

## <a name="filters"></a>Ajustar as comunicações cliente-servidor

A ligação de cliente é, normalmente, uma ligação HTTP básica, usando a biblioteca HTTP subjacente fornecida com o SDK Android.  Há vários motivos por que iria querer mudar isso:

* Pretender utilizar uma biblioteca HTTP alternativa para ajustar os tempos limite.
* Deseja fornecer uma barra de progresso.
* Que pretende adicionar um cabeçalho personalizado para suportar a funcionalidade de gestão de API.
* Gostaria de interceptar uma resposta com falhas para que pode implementar a reautenticação.
* Pretender registar pedidos de back-end para um serviço de análise.

### <a name="using-an-alternate-http-library"></a>Usando uma biblioteca HTTP alternativo

Chamar o `.setAndroidHttpClientFactory()` método imediatamente depois de criar a sua referência de cliente.  Por exemplo, para definir o tempo limite da ligação como 60 segundos (em vez do padrão de 10 segundos):

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

Pode implementar uma interceptação de cada solicitação implementando um `ServiceFilter`.  Por exemplo, a seguir atualiza uma barra de progresso previamente criada:

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

Pode anexar este filtro para o cliente da seguinte forma:

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>Personalizar os cabeçalhos de pedido

Utilize o seguinte procedimento `ServiceFilter` e anexar o filtro da mesma forma como o `ProgressFilter`:

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

Pode especificar uma estratégia de conversão que se aplica a todas as colunas, utilizando o [gson] [ 3] API. A biblioteca de cliente Android usa [gson] [ 3] em segundo plano para serializar objetos Java no dados JSON antes dos dados são enviados ao serviço de aplicações do Azure.  O seguinte código utiliza a **setFieldNamingStrategy()** método para definir a estratégia. Neste exemplo, irá eliminar o caráter inicial (uma "m") e, em seguida, em minúsculas o caráter seguinte, para cada nome de campo. Por exemplo, ele se transformaria "mId" em "id".  Implementar uma estratégia de conversão para reduzir a necessidade de `SerializedName()` anotações na maioria dos campos.

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

Esse código tem de ser executado antes de criar uma referência de cliente móvel utilizando o **MobileServiceClient**.

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: https://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
[Introdução à autenticação]: app-service-mobile-android-get-started-users.md
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
