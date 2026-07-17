// Source code is decompiled from a .class file using FernFlower decompiler (from Intellij IDEA).
import java.io.PrintStream;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.Statement;
import java.util.Scanner;

public class Locadora {
   static final String URL = "jdbc:mysql://localhost:3306/locadora";
   static final String USER = "root";
   static final String PASSWORD = "";
   static Scanner sc;

   public Locadora() {
   }

   public static Connection conectar() throws Exception {
      return DriverManager.getConnection("jdbc:mysql://localhost:3306/locadora", "root", "");
   }

   public static void cadastrarCliente() {
      try {
         Connection var0 = conectar();
         System.out.print("Nome: ");
         String var1 = sc.nextLine();
         System.out.print("CPF: ");
         String var2 = sc.nextLine();
         System.out.print("Telefone: ");
         String var3 = sc.nextLine();
         String var4 = "INSERT INTO cliente(nome,cpf,telefone) VALUES(?,?,?)";
         PreparedStatement var5 = var0.prepareStatement(var4);
         var5.setString(1, var1);
         var5.setString(2, var2);
         var5.setString(3, var3);
         var5.executeUpdate();
         System.out.println("Cliente cadastrado!");
         var0.close();
      } catch (Exception var6) {
         System.out.println(var6.getMessage());
      }

   }

   public static void cadastrarVeiculo() {
      try {
         Connection var0 = conectar();
         System.out.print("Modelo: ");
         String var1 = sc.nextLine();
         System.out.print("Placa: ");
         String var2 = sc.nextLine();
         System.out.print("Ano: ");
         int var3 = Integer.parseInt(sc.nextLine());
         String var4 = "INSERT INTO veiculo(modelo,placa,ano) VALUES(?,?,?)";
         PreparedStatement var5 = var0.prepareStatement(var4);
         var5.setString(1, var1);
         var5.setString(2, var2);
         var5.setInt(3, var3);
         var5.executeUpdate();
         System.out.println("Veículo cadastrado!");
         var0.close();
      } catch (Exception var6) {
         System.out.println(var6.getMessage());
      }

   }

   public static void registrarLocacao() {
      try {
         Connection var0 = conectar();
         System.out.print("ID Cliente: ");
         int var1 = Integer.parseInt(sc.nextLine());
         System.out.print("ID Veículo: ");
         int var2 = Integer.parseInt(sc.nextLine());
         System.out.print("Data da locação (AAAA-MM-DD): ");
         String var3 = sc.nextLine();
         String var4 = "INSERT INTO locacao(cliente_id,veiculo_id,data_locacao) VALUES(?,?,?)";
         PreparedStatement var5 = var0.prepareStatement(var4);
         var5.setInt(1, var1);
         var5.setInt(2, var2);
         var5.setString(3, var3);
         var5.executeUpdate();
         PreparedStatement var6 = var0.prepareStatement("UPDATE veiculo SET disponivel=false WHERE id=?");
         var6.setInt(1, var2);
         var6.executeUpdate();
         System.out.println("Locação registrada!");
         var0.close();
      } catch (Exception var7) {
         System.out.println(var7.getMessage());
      }

   }

   public static void registrarDevolucao() {
      try {
         Connection var0 = conectar();
         System.out.print("ID Veículo: ");
         int var1 = Integer.parseInt(sc.nextLine());
         System.out.print("Data da devolução (AAAA-MM-DD): ");
         String var2 = sc.nextLine();
         String var3 = "UPDATE locacao SET data_devolucao=? WHERE veiculo_id=? AND data_devolucao IS NULL";
         PreparedStatement var4 = var0.prepareStatement(var3);
         var4.setString(1, var2);
         var4.setInt(2, var1);
         var4.executeUpdate();
         PreparedStatement var5 = var0.prepareStatement("UPDATE veiculo SET disponivel=true WHERE id=?");
         var5.setInt(1, var1);
         var5.executeUpdate();
         System.out.println("Devolução registrada!");
         var0.close();
      } catch (Exception var6) {
         System.out.println(var6.getMessage());
      }

   }

   public static void veiculosDisponiveis() {
      try {
         Connection var0 = conectar();
         Statement var1 = var0.createStatement();
         ResultSet var2 = var1.executeQuery("SELECT * FROM veiculo WHERE disponivel=true");
         System.out.println("\nVEÍCULOS DISPONÍVEIS");

         while(var2.next()) {
            PrintStream var10000 = System.out;
            int var10001 = var2.getInt("id");
            var10000.println(var10001 + " - " + var2.getString("modelo") + " - " + var2.getString("placa"));
         }

         var0.close();
      } catch (Exception var3) {
         System.out.println(var3.getMessage());
      }

   }

   public static void veiculosAlugados() {
      try {
         Connection var0 = conectar();
         Statement var1 = var0.createStatement();
         ResultSet var2 = var1.executeQuery("SELECT * FROM veiculo WHERE disponivel=false");
         System.out.println("\nVEÍCULOS ALUGADOS");

         while(var2.next()) {
            PrintStream var10000 = System.out;
            int var10001 = var2.getInt("id");
            var10000.println(var10001 + " - " + var2.getString("modelo") + " - " + var2.getString("placa"));
         }

         var0.close();
      } catch (Exception var3) {
         System.out.println(var3.getMessage());
      }

   }

   public static void historicoCliente() {
      try {
         Connection var0 = conectar();
         System.out.print("ID do Cliente: ");
         int var1 = Integer.parseInt(sc.nextLine());
         String var2 = "SELECT c.nome,v.modelo,l.data_locacao,l.data_devolucao FROM locacao l JOIN cliente c ON c.id=l.cliente_id JOIN veiculo v ON v.id=l.veiculo_id WHERE c.id=?";
         PreparedStatement var3 = var0.prepareStatement(var2);
         var3.setInt(1, var1);
         ResultSet var4 = var3.executeQuery();

         while(var4.next()) {
            PrintStream var10000 = System.out;
            String var10001 = var4.getString("nome");
            var10000.println(var10001 + " | " + var4.getString("modelo") + " | " + String.valueOf(var4.getDate("data_locacao")) + " | " + String.valueOf(var4.getDate("data_devolucao")));
         }

         var0.close();
      } catch (Exception var5) {
         System.out.println(var5.getMessage());
      }

   }

   public static void quantidadeLocacoes() {
      try {
         Connection var0 = conectar();
         Statement var1 = var0.createStatement();
         ResultSet var2 = var1.executeQuery("SELECT v.modelo, COUNT(*) quantidade FROM locacao l JOIN veiculo v ON v.id=l.veiculo_id GROUP BY v.modelo");

         while(var2.next()) {
            PrintStream var10000 = System.out;
            String var10001 = var2.getString("modelo");
            var10000.println(var10001 + " -> " + var2.getInt("quantidade") + " locações");
         }

         var0.close();
      } catch (Exception var3) {
         System.out.println(var3.getMessage());
      }

   }

   public static void main(String[] var0) {
      int var1;
      do {
         System.out.println("\n===== LOCADORA =====");
         System.out.println("1 - Cadastrar Cliente");
         System.out.println("2 - Cadastrar Veículo");
         System.out.println("3 - Registrar Locação");
         System.out.println("4 - Registrar Devolução");
         System.out.println("5 - Veículos Disponíveis");
         System.out.println("6 - Veículos Alugados");
         System.out.println("7 - Histórico de Cliente");
         System.out.println("8 - Quantidade de Locações por Veículo");
         System.out.println("0 - Sair");
         System.out.print("Escolha: ");
         var1 = Integer.parseInt(sc.nextLine());
         switch (var1) {
            case 1:
               cadastrarCliente();
               break;
            case 2:
               cadastrarVeiculo();
               break;
            case 3:
               registrarLocacao();
               break;
            case 4:
               registrarDevolucao();
               break;
            case 5:
               veiculosDisponiveis();
               break;
            case 6:
               veiculosAlugados();
               break;
            case 7:
               historicoCliente();
               break;
            case 8:
               quantidadeLocacoes();
         }
      } while(var1 != 0);

      System.out.println("Sistema encerrado.");
   }

   static {
      sc = new Scanner(System.in);
   }
}
# Projeto-de-banco-de-dados
