# UPX5_-Dev_AguaSemDesperdicio
Aplicativo Para Identificar Vazamento de água em residencia
AndroideManifest

<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.UPX5_Desperdício_Agua"
        tools:targetApi="31">
        <activity
            android:name=".ResumoActivity"
            android:exported="false" />
        <activity
            android:name=".WaterConsumptionActivity"
            android:exported="false" />
        <activity
            android:name=".MainActivity"
            android:exported="true"
            android:theme="@style/Theme.UPX5_Desperdício_Agua">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name=".LoginActivity"
            android:exported="true" />
    </application>

</manifest>


Activity - Cadastro
MainActivity extends AppCompatActivity {

    private EditText etName, etEmail, etPassword;
    private AppDatabase database;
    
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        database = AppDatabase.getInstance(this);

        etName = findViewById(R.id.Name);
        etEmail = findViewById(R.id.Email);
        etPassword = findViewById(R.id.Password);
        Button btnRegister = findViewById(R.id.Register);

        btnRegister.setOnClickListener(view -> {
            String name = etName.getText().toString();
            String email = etEmail.getText().toString();
            String password = etPassword.getText().toString();

            if (!name.isEmpty() && !email.isEmpty() && !password.isEmpty()) {
                User user = new User(name, email, password);
                Executors.newSingleThreadExecutor().execute(() -> {
                    database.UserDao().insertUser(user);
                    runOnUiThread(() -> Toast.makeText(MainActivity.this, "Usuário cadastrado com sucesso!", Toast.LENGTH_SHORT).show());
                    // Redirecionar para a tela de Login
                    Intent intent = new Intent(MainActivity.this, LoginActivity.class);
                    startActivity(intent);
                    finish();
                });
            } else {
                Toast.makeText(MainActivity.this, "Por favor, preencha todos os campos", Toast.LENGTH_SHORT).show();



            }
        });
    }
}

Entidade

package com.example.upx5_desperdcio_agua;

import androidx.room.Entity;
import androidx.room.PrimaryKey;

@Entity(tableName = "user_table")
public class User {
    @PrimaryKey(autoGenerate = true)
    private int id;
    private String name;
    private String email;
    private String password;

    public User(String name, String email, String password) {
        this.name = name;
        this.email = email;
        this.password = password;
    }

    public int getId() { return id; }
    public void setId(int id) { this.id = id; }
    public String getName() { return name; }
    public String getEmail() { return email; }
    public String getPassword() { return password; }
}

Arquivo DAO

package com.example.upx5_desperdcio_agua;

import androidx.room.Dao;
import androidx.room.Insert;
import androidx.room.Query;

import com.example.upx5_desperdcio_agua.User;

@Dao
public interface UserDao {
    @Insert
    void insertUser(User user);

    @Query("SELECT * FROM user_table WHERE email = :email AND password = :password")
    User getUser(String email, String password);
}

XML

<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp">

    <EditText
        android:id="@+id/Name"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Nome" />

    <EditText
        android:id="@+id/Email"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Email" />

    <EditText
        android:id="@+id/Password"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Senha"
        android:inputType="textPassword" />

    <Button
        android:id="@+id/Register"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Cadastrar" />
</LinearLayout>


2 Activity - LOGIN

package com.example.upx5_desperdcio_agua;

import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;
import androidx.appcompat.app.AppCompatActivity;

public class LoginActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_login);

        // Referências aos componentes da tela
        EditText etUsername = findViewById(R.id.etUsername);
        EditText etPassword = findViewById(R.id.etPassword);
        Button btnLogin = findViewById(R.id.btnLogin);

        // Ação do botão de login
        btnLogin.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                String username = etUsername.getText().toString();
                String password = etPassword.getText().toString();

                // Verificação simples para exemplo
                if (username.equals("admin") && password.equals("1234")) {
                    Toast.makeText(LoginActivity.this, "Login realizado com sucesso!", Toast.LENGTH_SHORT).show();

                    // Redirecionar para outra tela (se necessário)
                    Intent intent = new Intent(LoginActivity.this, WaterConsumptionActivity.class);
                    startActivity(intent);
                    finish();

                } else {
                    Toast.makeText(LoginActivity.this, "Usuário ou senha inválidos!", Toast.LENGTH_SHORT).show();
                }
            }
        });
    }
}

XML
<?xml version="1.0" encoding="utf-8"?>

<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Login"
        android:textSize="24sp"
        android:layout_gravity="center"
        android:paddingBottom="24dp" />

    <EditText
        android:id="@+id/etUsername"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Username"
        android:inputType="text" />

    <EditText
        android:id="@+id/etPassword"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Password"
        android:inputType="textPassword"
        android:layout_marginTop="16dp" />

    <Button
        android:id="@+id/btnLogin"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Login"
        android:layout_marginTop="24dp" />
</LinearLayout>

3 Activity - Cadastrode Consumo

package com.example.upx5_desperdcio_agua;

import android.content.Intent;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;

import java.util.List;

public class WaterConsumptionActivity extends AppCompatActivity {

    private EditText etMonth;
    private EditText etYear;
    private EditText etWaterConsumption;
    private Button btnSaveConsumption;
    private Button buttonFinalizar;
    private AppDatabase database;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_water_consumption);

        // Inicializar o banco de dados
        database = AppDatabase.getInstance(this);

        // Associar os campos
        etWaterConsumption = findViewById(R.id.et_water_consumption);
        etYear = findViewById(R.id.et_year);
        etMonth = findViewById(R.id.et_month);
        btnSaveConsumption = findViewById(R.id.btn_save_consumption);
        buttonFinalizar = findViewById(R.id.btn_finish);

        // Configurar o botão de salvar
        btnSaveConsumption.setOnClickListener(v -> saveWaterConsumption());

        // Configurar o botão de finalizar
        buttonFinalizar.setOnClickListener(v -> {
            new Thread(() -> {
                // Obter todos os consumos do banco de dados
                List<Double> consumos = database.WaterConsumptionDao().getAllConsumptions();
                Double ultimoConsumo = database.WaterConsumptionDao().getLastConsumption();

                if (ultimoConsumo != null && !consumos.isEmpty()) {
                    double mediaConsumo = calcularMediaConsumo(consumos);
                    boolean isVazamento = verificarVazamento(consumos, ultimoConsumo);

                    runOnUiThread(() -> {
                        Intent intent = new Intent(WaterConsumptionActivity.this, ResumoActivity.class);
                        intent.putExtra("ultimoConsumo", ultimoConsumo);
                        intent.putExtra("mediaConsumo", mediaConsumo);
                        intent.putExtra("isVazamento", isVazamento);
                        startActivity(intent);
                    });
                } else {
                    runOnUiThread(() -> Toast.makeText(this, "Nenhum dado disponível para análise.", Toast.LENGTH_SHORT).show());
                }
            }).start();
        });
    }

    // Método auxiliar para salvar o consumo
    private void saveWaterConsumption() {
        String consumptionText = etWaterConsumption.getText().toString().trim();

        if (consumptionText.isEmpty()) {
            Toast.makeText(this, "Por favor, insira o consumo de água!", Toast.LENGTH_SHORT).show();
            return;
        }

        double consumption = Double.parseDouble(consumptionText);

        new Thread(() -> {
            try {
                String ano = etYear.getText().toString();
                String mes = etMonth.getText().toString();

                if (!ano.isEmpty() && !mes.isEmpty()) {
                    WaterConsumption data = new WaterConsumption();
                    data.setConsumption(consumption);
                    data.setAno(Integer.parseInt(ano));
                    data.setMes(Integer.parseInt(mes));

                    database.WaterConsumptionDao().insertConsumption(data);

                    // Limpar os campos na thread principal após salvar
                    runOnUiThread(() -> {
                        Toast.makeText(this, "Consumo salvo com sucesso!", Toast.LENGTH_SHORT).show();
                        etWaterConsumption.setText("");
                        etYear.setText("");
                        etMonth.setText("");
                    });
                } else {
                    runOnUiThread(() -> Toast.makeText(this, "Por favor, preencha Ano e Mês!", Toast.LENGTH_SHORT).show());
                }
            } catch (Exception e) {
                runOnUiThread(() -> Toast.makeText(this, "Erro ao salvar consumo: " + e.getMessage(), Toast.LENGTH_SHORT).show());
            }
        }).start();
    }

    // Métodos auxiliares para calcular a média e verificar vazamento
    private double calcularMediaConsumo(List<Double> consumos) {
        if (consumos.isEmpty()) return 0.0;
        double soma = 0.0;
        for (double consumo : consumos) {
            soma += consumo;
        }
        return soma / consumos.size();
    }

    private boolean verificarVazamento(List<Double> consumos, double ultimoConsumo) {
        double mediaConsumo = calcularMediaConsumo(consumos);
        double limiteVazamento = mediaConsumo * 1.3;
        return ultimoConsumo > limiteVazamento;
    }
}

Entidade

package com.example.upx5_desperdcio_agua;

import androidx.room.Entity;
import androidx.room.PrimaryKey;


@Entity (tableName = "water_consumption")
public class WaterConsumption {
    @PrimaryKey(autoGenerate = true)
    private int id;
    private double consumption;
    private int ano;
    private int mes;

    // Getters e Setters
    public int getId() { return id; }
    public void setId(int id) { this.id = id; }

    public double getConsumption() { return consumption; }
    public void setConsumption(double consumption) { this.consumption = consumption; }

    public int getAno() {return ano;}
    public void setAno(int ano) {this.ano = ano;}

    public int getMes() {return mes;}
    public void setMes(int mes) {this.mes = mes;}
}


Arquivo DAO

package com.example.upx5_desperdcio_agua;

import androidx.room.Dao;
import androidx.room.Insert;
import androidx.room.Query;
import java.util.List;

@Dao
public interface WaterConsumptionDao {
    @Insert
    void insertConsumption(WaterConsumption consumption);

    @Query("SELECT consumption FROM water_consumption")
    List<Double> getAllConsumptions();

    @Query("SELECT consumption FROM water_consumption ORDER BY id DESC LIMIT 1")
    Double getLastConsumption(); // Retorna o último consumo registrado

    XML

    <LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp">

    <!-- Campo para o consumo -->

    <!-- Botão para salvar -->

    <!-- Campo para o mês -->
    <EditText
        android:id="@+id/et_water_consumption"
        android:layout_width="454dp"
        android:layout_height="99dp"
        android:hint="Consumo de água (litros)"
        android:inputType="numberDecimal" />

    <EditText
        android:id="@+id/et_month"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Mês"
        android:inputType="text" />

    <!-- Campo para o ano -->
    <EditText
        android:id="@+id/et_year"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Ano"
        android:inputType="number" />

    <Button
        android:id="@+id/btn_save_consumption"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Salvar Consumo" />

    <!-- Botão para finalizar -->
    <Button
        android:id="@+id/btn_finish"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Finalizar" />

</LinearLayout>

4 Activity - Resumo

import androidx.appcompat.app.AppCompatActivity;

import com.example.upx5_desperdcio_agua.R;

public class ResumoActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_resumo);

        // Referências aos TextViews
        TextView consumoText = findViewById(R.id.consumoText);
        TextView mediaText = findViewById(R.id.mediaText);
        TextView statusText = findViewById(R.id.statusText);
        Button voltarButton = findViewById(R.id.voltarButton);

        // Receber os dados da Intent
        Intent intent = getIntent();
        double ultimoConsumo = intent.getDoubleExtra("ultimoConsumo", 0.0);
        double mediaConsumo = intent.getDoubleExtra("mediaConsumo", 0.0);
        boolean isVazamento = intent.getBooleanExtra("isVazamento", false);

        // Atualizar os TextViews com os dados recebidos
        consumoText.setText("Último Consumo: " + ultimoConsumo + " L");
        mediaText.setText("Média de Consumo: " + mediaConsumo + " L");
        statusText.setText(isVazamento ? "Status: Possível vazamento detectado!" : "Status: Consumo normal.");

        // Configurar o botão Voltar
        voltarButton.setOnClickListener(v -> finish());
    }
}

Banco de dados

package com.example.upx5_desperdcio_agua;

import android.content.Context;
import androidx.room.Database;
import androidx.room.Room;
import androidx.room.RoomDatabase;

@Database(entities = {User.class, WaterConsumption.class}, version = 2)
public abstract class AppDatabase extends RoomDatabase {
    private static AppDatabase INSTANCE;
    public abstract UserDao UserDao();
    public abstract WaterConsumptionDao WaterConsumptionDao();


        public static synchronized AppDatabase getInstance(Context context){
            if (INSTANCE == null) {
                INSTANCE = Room.databaseBuilder(context.getApplicationContext(),
                                AppDatabase.class, "app_database")
                        .fallbackToDestructiveMigration()
                        .build();
            }
            return INSTANCE;
        }


}
