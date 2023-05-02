# DBRoomWithView

Lab Databases Application
Objectives:

    Create databases
    Access databases
    Update databases
    Utilize RecyclerView
    Design and implement GUI to interact with databases
    Integrate with Android Architecture guidelines to build database storage application

Project:
This project will display data alphabetically and allow users to create new record.




READ THIS:  Make sure you upgrade your IDE as well as your SDK, Emulator and Gradle to the latest version. At least to API level 31.
Architecture Overview
The following diagram shows all the pieces of the app. Each of the enclosing boxes (except for the SQLite database) represents a class that you will create.





Steps:
Step 1: Create an Android project with Empty Activity and name it “DBRoomWithView”. Note we use Java as the programming language.
Step 2: Update gradle files (including Add Dependencies)
Step 2.1: Under “Gradle Scripts”, open build.gradle(Module:)

    Add the following compile options inside the android block if it does not exist.

compileOptions {
sourceCompatibility = 1.8
targetCompatibility = 1.8
}



    (If your Sdk Version is higher than 31 then no need to change it. But make sure your Emulator is with API level 31 at least. See video demonstration.) Change compileSdk to 31 inside the android block, i.e.,

compileSdk 31.



    Replace the dependencies block with:
     
    dependencies {
        implementation "androidx.appcompat:appcompat:$rootProject.appCompatVersion"

        // Dependencies for working with Architecture components
        // You'll probably have to update the version numbers in build.gradle (Project)

        // Room components
        implementation "androidx.room:room-runtime:$rootProject.roomVersion"
        annotationProcessor "androidx.room:room-compiler:$rootProject.roomVersion"
        androidTestImplementation "androidx.room:room-testing:$rootProject.roomVersion"

        // Lifecycle components
        implementation "androidx.lifecycle:lifecycle-viewmodel:$rootProject.lifecycleVersion"
        implementation "androidx.lifecycle:lifecycle-livedata:$rootProject.lifecycleVersion"
        implementation "androidx.lifecycle:lifecycle-common-java8:$rootProject.lifecycleVersion"

        // UI
        implementation "androidx.constraintlayout:constraintlayout:$rootProject.constraintLayoutVersion"
        implementation "com.google.android.material:material:$rootProject.materialVersion"

        // Testing
        testImplementation "junit:junit:$rootProject.junitVersion"
        androidTestImplementation "androidx.arch.core:core-testing:$rootProject.coreTestingVersion"
        androidTestImplementation ("androidx.test.espresso:espresso-core:$rootProject.espressoVersion", {
            exclude group: 'com.android.support', module: 'support-annotations'
        })
        androidTestImplementation "androidx.test.ext:junit:$rootProject.androidxJunitVersion"
    }


Step 2.2: Open build.gradle(Project:) file and add version numbers to the end of the file
ext {
appCompatVersion = '1.5.1'
constraintLayoutVersion = '2.1.4'
coreTestingVersion = '2.1.0'
lifecycleVersion = '2.3.1'
materialVersion = '1.3.0'
roomVersion = '2.3.0'
// testing
junitVersion = '4.13.2'
espressoVersion = '3.4.0'
androidxJunitVersion = '1.1.2'
}

Of course, you can update to the latest versions. But this set of versions works fine.
Click “Sync Now” on the upper right hand corner
You need to click Tools > SDK Manager, in the SDK Platform tab, select Android 12 to download API 31
After that, build an emulator with API 31. You need to download API 31 image.
In general, you can use File > Project Structure to view and edit your project configuration
For more information on adding dependencies you can read “Adding Components to your Project” at https://developer.android.com/topic/libraries/architecture/adding-components.html
Step 3: Create an entity (i.e., table) to hold data (i.e., words). Each property represents a column in the table. Room uses these properties to create table and instantiate objects.
Step 3.1: Create a Java file and name it as “Word”
Step 3.2: Define the properties to the class in Java code as follows:

private String mWord;

public Word(@NonNull String word) {this.mWord = word;}

public String getWord(){return this.mWord;}


The class should like the following:

package com.example.dbroomwithview;



import androidx.annotation.NonNull;



public class Word {

    private String mWord;

    public Word(@NonNull String word) {this.mWord = word;}

    public String getWord(){return this.mWord;}

}



I hope you have defined your package as mine.
Of course I understand your package name might be different. Thus, I will not keep mentioning that in the lab.
Though I will not show the package name in the lab, it does not mean you don’t need it.
I just try not to confuse you if you choose to use a different name.
Step 3.3: Annotate the class so that it is meaningful to a Room database. Update Word class with annotations as shown below:

import androidx.annotation.NonNull;

import androidx.room.ColumnInfo;

import androidx.room.Entity;

import androidx.room.PrimaryKey;



@Entity(tableName = "Word")

public class Word {



    @PrimaryKey

    @NonNull

    @ColumnInfo(name="word")

    private String mWord;



    public Word(@NonNull String word) {this.mWord = word;}



    public String getWord(){return this.mWord;}

}



When you type in the annotations, you can use Alt+Enter on Windows/Linux or Option+Enter on Mac to import the corresponding libraries.
Step 4: Create DAO to provide queries for:

    Get all words ordered alphabetically
    Insert a word
    Delete all words


Step 4.1: Create a java class, WordDao, and make sure you select Interface in the “New Java Class” dialog.
Otherwise, you will create a class instead of interface. If that is the case, just make sure you change the whole declaration. See the code in Step 4.2.

Step 4.2: You can copy the following code to your class. But make sure you fix and import necessary libraries.


@Dao

public interface WordDao {



// allowing the insert of the same word multiple times by passing a

// conflict resolution strategy

@Insert(onConflict = OnConflictStrategy.IGNORE)

void insert(Word word);



@Query("DELETE FROM Word")

void deleteAll();



@Query("SELECT * FROM Word ORDER BY word ASC")

List<Word> getAlphabetizedWords();

}


After fixing your statements, your code should look like the following:


import androidx.room.Dao;

import androidx.room.Insert;

import androidx.room.OnConflictStrategy;

import androidx.room.Query;



import java.util.List;



@Dao

public interface WordDao {



    // allowing the insert of the same word multiple times by passing a
     // conflict resolution strategy
     @Insert(onConflict = OnConflictStrategy.IGNORE)

    void insert(Word word);



    @Query("DELETE FROM Word")

    void deleteAll();



    @Query("SELECT * FROM Word ORDER BY word ASC")

    List<Word> getAlphabetizedWords();

}


Again, I did not include package name here. But your class should have it. I will not remind it any more in the lab.

For resolving conflict, see https://developer.android.com/reference/androidx/room/OnConflictStrategy.html

Ignore indicates it will not be inserted and -1 will be returned when there is any conflict while inserting a record.

For simplicity and demonstration only we won’t touch how to delete a record for the time being. But it could be implemented easily.

Step 5: Create a LiveData Class
In order to make data observable, we utilize LiveData.


Step 5.1: In WordDao, wrap List<Word> with LiveData by changing

List<Word> getAlphabetizedWords();

to
LiveData<List<Word>> getAlphabetizedWords();

Of course, you need to use Alt+Enter to import the LiveData library.

Now the WordDao class  should look like as follows:


import androidx.lifecycle.LiveData;

import androidx.room.Dao;

import androidx.room.Insert;

import androidx.room.OnConflictStrategy;

import androidx.room.Query;



import java.util.List;



@Dao

public interface WordDao {



    // allowing the insert of the same word multiple times by passing a
     // conflict resolution strategy
     @Insert(onConflict = OnConflictStrategy.IGNORE)

    void insert(Word word);



    @Query("DELETE FROM Word")

    void deleteAll();



    @Query("SELECT * FROM Word ORDER BY word ASC")

    LiveData<List<Word>> getAlphabetizedWords();

}


Step 6: Create a Room database class

Step 6.1: Create a Java class, WordRoomDatabase
Step 6.2: Make the class as follows:


@Database(entities = {Word.class}, version = 1, exportSchema = false)

public abstract class WordRoomDatabase extends RoomDatabase {



public abstract WordDao wordDao();



private static volatile WordRoomDatabase INSTANCE;

private static final int NUMBER_OF_THREADS = 4;

static final ExecutorService databaseWriteExecutor =

        Executors.newFixedThreadPool(NUMBER_OF_THREADS);



static WordRoomDatabase getDatabase(final Context context) {

        if (INSTANCE == null) {

            synchronized (WordRoomDatabase.class) {

                if (INSTANCE == null) {

                    INSTANCE = Room.databaseBuilder(context.getApplicationContext(),

                            WordRoomDatabase.class, "word_database")

                            .build();

                }

            }

        }

        return INSTANCE;

    }

}



Of course, you need to fix and import libraries. Make sure you still have package name there.
Step 7: Create Repository class
Step 7.1: Create a Java class, WordRepository.
Step 7.2: Make the class as follows :

public class WordRepository {



private WordDao mWordDao;

private LiveData<List<Word>> mAllWords;



        // Note that in order to unit test the WordRepository, you have to remove the Application
         // dependency. This adds complexity and much more code, and this sample is not about testing.
         // See the BasicSample in the android-architecture-components repository at
         // https://github.com/googlesamples
         WordRepository(Application application) {

        WordRoomDatabase db = WordRoomDatabase.getDatabase(application);

        mWordDao = db.wordDao();

        mAllWords = mWordDao.getAlphabetizedWords();

        }



        // Room executes all queries on a separate thread.
         // Observed LiveData will notify the observer when the data has changed.
         LiveData<List<Word>> getAllWords() {

        return mAllWords;

        }



        // You must call this on a non-UI thread or your app will throw an exception. Room ensures
         // that you're not doing any long running operations on the main thread, blocking the UI.
         void insert(Word word) {

        WordRoomDatabase.databaseWriteExecutor.execute(() -> {

        mWordDao.insert(word);

        });

        }

}


Again, you need to use Alt+Enter to fix and import libraries. (Don’t forget that Package name has to be there if you copy and paste the code.)
Step 8: Implement ViewModel
Step 8.1: Create a Java file, WordViewModel
Step 8.2: Change the class to be the one as follows:

public class WordViewModel extends AndroidViewModel {



    private WordRepository mRepository;



    private final LiveData<List<Word>> mAllWords;



    public WordViewModel (Application application) {

        super(application);

        mRepository = new WordRepository(application);

        mAllWords = mRepository.getAllWords();

    }



    LiveData<List<Word>> getAllWords() { return mAllWords; }



    public void insert(Word word) { mRepository.insert(word); }

}


Of course you need to fix it to import libraries and make sure you do not delete the package name.
So after the fixing the code should look like:

package com.example.dbroomwithview;



import android.app.Application;



import androidx.lifecycle.AndroidViewModel;

import androidx.lifecycle.LiveData;



import java.util.List;



public class WordViewModel extends AndroidViewModel {



    private WordRepository mRepository;



    private final LiveData<List<Word>> mAllWords;



    public WordViewModel (Application application) {

        super(application);

        mRepository = new WordRepository(application);

        mAllWords = mRepository.getAllWords();

    }



    LiveData<List<Word>> getAllWords() { return mAllWords; }



    public void insert(Word word) { mRepository.insert(word); }

}


Notice that insert uses the insert defined in repository to provide encapsulation from the UI
Use AndroidViewModel, instead of ViewModel, if you need the ViewModel to stay alive during the whole application to prevent memory leak.
About time to deal with user interface.
Step 9: Add XML layout for the list and items because we will use recyclerView
Step 9.1: Add the following block into the themes.xml under the directory values/themes.  Put the following statements inside <resources> …</resources>

<!-- The default font for RecyclerView items is too small.

    The margin is a simple delimiter between the words. -->

    <style name="word_title">

        <item name="android:layout_width">match_parent</item>

        <item name="android:layout_marginBottom">8dp</item>

        <item name="android:paddingLeft">8dp</item>

        <item name="android:background">@android:color/holo_orange_light</item>

        <item name="android:textAppearance">@android:style/TextAppearance.Large</item>

    </style>



Let’s ignore the night themes for this lab.

Step 9.2: Create a layout file, recyclerview_item.xml under layout directory, as follows:
Right click layout > New > Layout Resource File, put “recyclerview_item” into the File name then click OK button.
For simplicity, we will not work on Component Tree this time. Go to the recyclerview_item.xml and replace its code by copying the following statements into it:

<?xml version="1.0" encoding="utf-8"?>

<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"

    android:orientation="vertical" 

    android:layout_width="match_parent"

    android:layout_height="wrap_content">



    <TextView

        android:id="@+id/textView"

        style="@style/word_title"

        android:layout_width="match_parent"

        android:layout_height="wrap_content"

        android:background="@android:color/holo_orange_light" />

</LinearLayout>


Step 9.3: In activity_main.xml under layout directory, replace TextView with a RecyclerView and add a floating button (FAB). Your layout file should look like this:

<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"

    xmlns:app="http://schemas.android.com/apk/res-auto"

    xmlns:tools="http://schemas.android.com/tools"

    android:layout_width="match_parent"

    android:layout_height="match_parent"

    tools:context=".MainActivity">



    <androidx.recyclerview.widget.RecyclerView

        android:id="@+id/recyclerview"

        android:layout_width="0dp"

        android:layout_height="0dp"

        app:layout_constraintBottom_toBottomOf="parent"

        app:layout_constraintLeft_toLeftOf="parent"

        app:layout_constraintRight_toRightOf="parent"

        app:layout_constraintTop_toTopOf="parent"

        tools:listitem="@layout/recyclerview_item" />



    <com.google.android.material.floatingactionbutton.FloatingActionButton

        android:id="@+id/fab"

        android:layout_width="wrap_content"

        android:layout_height="wrap_content"

        android:layout_margin="16dp"

        android:clickable="true"

        app:layout_constraintBottom_toBottomOf="parent"

        app:layout_constraintEnd_toEndOf="parent"

        app:srcCompat="@android:drawable/ic_input_add" />



</androidx.constraintlayout.widget.ConstraintLayout>


You can either put the code in or go to Component Tree and drag and drop a RecyclerView, configure it, and then drag and drop a FloatingActionButton (FAB) and then configure it with the attributes listed above. If you create the GUI yourself, make sure the FAB is on the same level with the RecyclerView and not within the RecyclerView.
Step 10: Add a RecyclerView
Step 10.1: Create a Java file, WordViewHolder
Step 10.2: Copy the following code for WordViewHolder (Of course you can create the file and create bind method yourself).

class WordViewHolder extends RecyclerView.ViewHolder {

    private final TextView wordItemView;



    private WordViewHolder(View itemView) {

        super(itemView);

        wordItemView = itemView.findViewById(R.id.textView);

    }



    public void bind(String text) {

        wordItemView.setText(text);

    }



    static WordViewHolder create(ViewGroup parent) {

        View view = LayoutInflater.from(parent.getContext())

                .inflate(R.layout.recyclerview_item, parent, false);

        return new WordViewHolder(view);

    }



}


Notice I take the public off. So this is not a public class. You need to fix and use Alt+Enter to import libraries.
Your file should look like the following in the end:

package com.example.dbroomwithview;



import android.view.LayoutInflater;

import android.view.View;

import android.view.ViewGroup;

import android.widget.TextView;



import androidx.recyclerview.widget.RecyclerView;



class WordViewHolder extends RecyclerView.ViewHolder {

    private final TextView wordItemView;



    private WordViewHolder(View itemView) {

        super(itemView);

        wordItemView = itemView.findViewById(R.id.textView);

    }



    public void bind(String text) {

        wordItemView.setText(text);

    }



    static WordViewHolder create(ViewGroup parent) {

        View view = LayoutInflater.from(parent.getContext())

                .inflate(R.layout.recyclerview_item, parent, false);

        return new WordViewHolder(view);

    }



}


Step 10.3: Create a Java file, WordListAdapter, that extends ListAdapter. Create DiffUtil.ItemCallback implementing as a static class in WordListAdapter class as follows:

public class WordListAdapter extends ListAdapter<Word, WordViewHolder> {



    public WordListAdapter(@NonNull DiffUtil.ItemCallback<Word> diffCallback) {

        super(diffCallback);

    }



    @Override

    public WordViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {

        return WordViewHolder.create(parent);

    }



    @Override

    public void onBindViewHolder(WordViewHolder holder, int position) {

        Word current = getItem(position);

        holder.bind(current.getWord());

    }



    static class WordDiff extends DiffUtil.ItemCallback<Word> {



        @Override

        public boolean areItemsTheSame(@NonNull Word oldItem, @NonNull Word newItem) {

            return oldItem == newItem;

        }



        @Override

        public boolean areContentsTheSame(@NonNull Word oldItem, @NonNull Word newItem) {

            return oldItem.getWord().equals(newItem.getWord());

        }

    }

}



After fixing with Alt+Enter, your code should look like:

package com.example.dbroomwithview;



import android.view.ViewGroup;



import androidx.annotation.NonNull;

import androidx.recyclerview.widget.DiffUtil;

import androidx.recyclerview.widget.ListAdapter;



public class WordListAdapter extends ListAdapter<Word, WordViewHolder> {



    public WordListAdapter(@NonNull DiffUtil.ItemCallback<Word> diffCallback) {

        super(diffCallback);

    }



    @Override

    public WordViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {

        return WordViewHolder.create(parent);

    }



    @Override

    public void onBindViewHolder(WordViewHolder holder, int position) {

        Word current = getItem(position);

        holder.bind(current.getWord());

    }



    static class WordDiff extends DiffUtil.ItemCallback<Word> {



        @Override

        public boolean areItemsTheSame(@NonNull Word oldItem, @NonNull Word newItem) {

            return oldItem == newItem;

        }



        @Override

        public boolean areContentsTheSame(@NonNull Word oldItem, @NonNull Word newItem) {

            return oldItem.getWord().equals(newItem.getWord());

        }

    }

}


Step 10.4: Add the RecyclerView in the onCreate() method of MainActivity.
Add the following code in onCreate() underneath setContentView():

RecyclerView recyclerView = findViewById(R.id.recyclerview);
final WordListAdapter adapter = new WordListAdapter(new WordListAdapter.WordDiff());
recyclerView.setAdapter(adapter);
recyclerView.setLayoutManager(new LinearLayoutManager(this));

Of course, you need to use Alt+Enter to fix and import libraries.
Step 11: Run your app with an emulator with the latest API level to test your work so far though we have not populate data yet.
Step 12: Populate database
Step 12.1: Go to WordRoomDatabase.java file
Step 12.2: Create a call back method to delete contents of the database and populate “Hello” and “Android”. Notice thread is used because you don’t want to execute any long operation on UI thread.
private static RoomDatabase.Callback sRoomDatabaseCallback = new RoomDatabase.Callback() {
@Override
public void onCreate(@NonNull SupportSQLiteDatabase db) {
super.onCreate(db);

        // If you want to keep data through app restarts,
        // comment out the following block
        databaseWriteExecutor.execute(() -> {
            // Populate the database in the background.
            // If you want to start with more words, just add them.
            WordDao dao = INSTANCE.wordDao();
            dao.deleteAll();

            Word word = new Word("Hello");
            dao.insert(word);
            word = new Word("Android");
            dao.insert(word);
        });
    }
};

Step 12.3: Add code so that this Callback will be execute before the .build() method is invoked.
That is, your getDatabase() should like as follows:

static WordRoomDatabase getDatabase(final Context context) {

    if (INSTANCE == null) {

        synchronized (WordRoomDatabase.class) {

            if (INSTANCE == null) {

                INSTANCE = Room.databaseBuilder(context.getApplicationContext(),

                        WordRoomDatabase.class, "word_database")

                        .addCallback(sRoomDatabaseCallback)

                        .build();

            }

        }

    }

    return INSTANCE;

}


Notice the statement
.addCallback(sRoomDatabaseCallback)

is added before .build()
Step 13: Create an activity to insert word
Step 13.1: Add the following elements into values/strings.xml

<string name="hint_word">Word...</string>

<string name="button_save">Save</string>

<string name="empty_not_saved">Word not saved because it is empty.</string>


That is, your strings.xml should look like:

<resources>

    <string name="app_name">DBRoomWithView</string>

    <string name="hint_word">Word...</string>

    <string name="button_save">Save</string>

    <string name="empty_not_saved">Word not saved because it is empty.</string>

    <string name="add_word">Add word</string>

</resources>


Step 13.2: Add the following resources to value/colors.xml

<color name="buttonLabel">#FFFFFF</color>

<color name="colorPrimary">#3F51B5</color>

<color name="colorPrimaryDark">#303F9F</color>

<color name="colorAccent">#FF4081</color>


Step 13.3: Create a new dimension resource file
Step 13.3.1: Select File > New > Android Resource File. If you see Value Resource File, it is fine. It all depends on where your current selected object under the project.
Step 13.3.2: From the “Available qualifiers”, just select Dimension (DONOT click >> button)
Step 13.3.3: set the File Name field to be “dimens” (without double quotes)
Step 13.3.4: Click OK button
This will create a dimens.xml file under res/values
Step 13.3.5: Add the three dimension resources in dimens.xml
The dimens.xml should look like:

<?xml version="1.0" encoding="utf-8"?>
 <resources>
    <dimen name="small_padding">8dp</dimen>
    <dimen name="big_padding">16dp</dimen>

    <dimen name="min_height">48dp</dimen>
</resources>


Step 13.3.6: In activity_main.xml, set up the padding for RecyclerView to be @dimen/big_padding
You can use the attributes panel or just add the following xml element to the RecyclerView:

android:padding="@dimen/big_padding"




Step 14 Set up another activity to insert new word
Step 14.1: Use Empty Activity template to create an Activity and name it as “NewWordActivity”
Step 14.2: In order to keep it simple, just change the activity_new_word.xml as follows:

<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"

    android:orientation="vertical"

    android:layout_width="match_parent"

    android:layout_height="match_parent">



    <EditText

        android:id="@+id/edit_word"

        android:layout_width="match_parent"

        android:layout_height="wrap_content"

        android:minHeight="@dimen/min_height"

        android:fontFamily="sans-serif-light"

        android:hint="@string/hint_word"

        android:inputType="textAutoComplete"

        android:layout_margin="@dimen/big_padding"

        android:textSize="18sp" />



    <Button

        android:id="@+id/button_save"

        android:layout_width="match_parent"

        android:layout_height="wrap_content"

        android:background="@color/colorPrimary"

        android:text="@string/button_save"

        android:layout_margin="@dimen/big_padding"

        android:textColor="@color/buttonLabel" />



</LinearLayout>


Step 14.3: Change the NewWordActivity.java as follows: (Make sure you delete the original onCreate())



public static final String EXTRA_REPLY = "com.example.android.wordlistsql.REPLY";



private EditText mEditWordView;



@Override

public void onCreate(Bundle savedInstanceState) {

    super.onCreate(savedInstanceState);

    setContentView(R.layout.activity_new_word);

    mEditWordView = findViewById(R.id.edit_word);



    final Button button = findViewById(R.id.button_save);

    button.setOnClickListener(view -> {

        Intent replyIntent = new Intent();

        if (TextUtils.isEmpty(mEditWordView.getText())) {

            setResult(RESULT_CANCELED, replyIntent);

        } else {

            String word = mEditWordView.getText().toString();

            replyIntent.putExtra(EXTRA_REPLY, word);

            setResult(RESULT_OK, replyIntent);

        }

        finish();

    });

}


Again you need to use Alt+Enter to fix and import libraries.
Step 15: The last step is to connect the UI in main activity to database
Step 15.1: Open MainActivity.java
Step 15.2: Declare a member variable for ViewModel

private WordViewModel mWordViewModel;


Step 15.3: Use ViewModelProvider to provide a ViewModel and associate the ViewModel with activity. Add the following code below the RecyclerView block in onCreate():

mWordViewModel = new ViewModelProvider(this).get(WordViewModel.class);

mWordViewModel.getAllWords().observe(this, words -> {

    // Update the cached copy of the words in the adapter.
     adapter.submitList(words);

});


Step 15.4: Define a member variable as a request code in MainActivity

public static final int NEW_WORD_ACTIVITY_REQUEST_CODE = 1;



Step 15.5: Define a callback to handle result coming back from NewWordActivity. This is another method. DON’T put this block inside onCreate().



public void onActivityResult(int requestCode, int resultCode, Intent data) {

    super.onActivityResult(requestCode, resultCode, data);



    if (requestCode == NEW_WORD_ACTIVITY_REQUEST_CODE && resultCode == RESULT_OK) {

        Word word = new Word(data.getStringExtra(NewWordActivity.EXTRA_REPLY));

        mWordViewModel.insert(word);

    } else {

        Toast.makeText(

                getApplicationContext(),

                R.string.empty_not_saved,

                Toast.LENGTH_LONG).show();

    }

}





Step 15.6: Define event handler for FloatingActionButton to insert new word when the user taps the FAB. Add the following code in onCreate()

FloatingActionButton fab = findViewById(R.id.fab);

fab.setOnClickListener( view -> {

    Intent intent = new Intent(MainActivity.this, NewWordActivity.class);

    startActivityForResult(intent, NEW_WORD_ACTIVITY_REQUEST_CODE);

});


The final version of the main activity should look like as follows:

package com.example.dbroomwithview;



import androidx.appcompat.app.AppCompatActivity;

import androidx.lifecycle.ViewModelProvider;

import androidx.recyclerview.widget.LinearLayoutManager;

import androidx.recyclerview.widget.RecyclerView;



import android.content.Intent;

import android.os.Bundle;

import android.widget.Toast;



import com.google.android.material.floatingactionbutton.FloatingActionButton;



public class MainActivity extends AppCompatActivity {

    private WordViewModel mWordViewModel;

    public static final int NEW_WORD_ACTIVITY_REQUEST_CODE = 1;





    @Override

    protected void onCreate(Bundle savedInstanceState) {

        super.onCreate(savedInstanceState);

        setContentView(R.layout.activity_main);



        RecyclerView recyclerView = findViewById(R.id.recyclerview);

 

        final WordListAdapter adapter = new WordListAdapter(new WordListAdapter.WordDiff());

        recyclerView.setAdapter(adapter);

        recyclerView.setLayoutManager(new LinearLayoutManager(this));



        mWordViewModel = new ViewModelProvider(this).get(WordViewModel.class);

        mWordViewModel.getAllWords().observe(this, words -> {

            // Update the cached copy of the words in the adapter.
             adapter.submitList(words);

        });



        FloatingActionButton fab = findViewById(R.id.fab);

        fab.setOnClickListener( view -> {

            Intent intent = new Intent(MainActivity.this, NewWordActivity.class);

            startActivityForResult(intent, NEW_WORD_ACTIVITY_REQUEST_CODE);

        });





    }



    public void onActivityResult(int requestCode, int resultCode, Intent data) {

        super.onActivityResult(requestCode, resultCode, data);



        if (requestCode == NEW_WORD_ACTIVITY_REQUEST_CODE && resultCode == RESULT_OK) {

            Word word = new Word(data.getStringExtra(NewWordActivity.EXTRA_REPLY));

            mWordViewModel.insert(word);

        } else {

            Toast.makeText(

                    getApplicationContext(),

                    R.string.empty_not_saved,

                    Toast.LENGTH_LONG).show();

        }

    }



}


Step 16: Run the app. You will see “Android” and “Hello” are shown. Then you can click the FloatingActionButton to insert new words one at a time.
Submission: Attach the following screenshots to a Window Word document and upload the Word document

    the default screen with “Android” and “Hello”
    a screenshot with the first word you enter
    a screenshot with the second word you enter
