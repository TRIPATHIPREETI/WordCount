# WordCount
--------------------------------------------------------------------------------------------
package wordutilityfile;

import java.io.BufferedReader;
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;
import java.util.*;
import java.sql.*;

public class Wordfile {

	/**
	 * Database Connectivity
	 */
	public static void main(String[] args) throws ClassNotFoundException {

		try {

			final String JDBC_DRIVER = "com.mysql.jdbc.Driver";
			final String DB_URL = "jdbc:mysql://localhost/words";

			String USER = "root";
			String PASS = "ROOTIMPETUS";
			Connection conn = null;
			Statement stmt = null;
			Class.forName("com.mysql.jdbc.Driver");
			System.out.println("Connecting to database...");
			conn = DriverManager.getConnection(DB_URL, USER, PASS);

			/* Reading input and stop word text file */

			String line1;
			String line2;
			String input[];
			String stopwords[];

			/* Reading input file */

			FileReader reader1 = new FileReader(
					"/home/impadmin/Desktop/dummyfile");
			BufferedReader bufferedReader1 = new BufferedReader(reader1);

			while ((line1 = bufferedReader1.readLine()) != null) {
				input = line1.split(" ");
				for (int i = 0; i < input.length; i++) {
					// System.out.println(input[i]);
				}
				/* Reading stop words file */

				FileReader reader2 = new FileReader(
						"/home/impadmin/Desktop/stopwordslist.txt");
				BufferedReader bufferedReader2 = new BufferedReader(reader2);
				
				int count = 0;
				stopwords = new String[2000];
				while ((line2 = bufferedReader2.readLine()) != null) {
					stopwords[count] = line2;
					// System.out.println(stopwords[count]);
					count++;
				}

				/* comparing the two arrays */
				int flag = 0;
				for (int i = 0; i < input.length; i++) {
					flag = 0;
					for (int j = 0; j < stopwords.length; j++) {
						if (input[i].equals(stopwords[j])) {
							System.out.println("it contains stop words : "
									+ input[i]);

							flag = 1;
							break;
						}

					}
					if (flag == 0) {
						//System.out.println("Creating statement...");
						stmt = conn.createStatement();
						String sql;
						sql = "SELECT * FROM list";
						ResultSet rs = stmt.executeQuery(sql);
						
						int check_flag=0;
						while (rs.next()) {
							
							String word = rs.getString("word");

							if (word.equals(input[i])) {
								int countt = rs.getInt("count");
								
								sql = "UPDATE list SET count = '"+ ++countt + "'  WHERE word = '" + input[i] + "';";
								stmt.executeUpdate(sql);
								check_flag=1;
								//rs.close();
								break;
							}
							
							// System.out.println(word);

						}
						if(check_flag==0){
						sql = "INSERT INTO list (word) VALUES ( '" + input[i]
								+ "' );";
						stmt.executeUpdate(sql);
						}
					}

				}
			}

		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
