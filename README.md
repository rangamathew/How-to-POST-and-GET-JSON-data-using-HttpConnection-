# How-to-POST-and-GET-JSON-data-using-HttpConnection-
We have optimise the HTTP connection code, In this code we are writing code one time for doInBackground() function for every GET and POST request. Optimised HTTP Connection code for Parsing which developer can be used multiple times without rewriting the whole override function.   

Step 1: Create ApiHit class for common use for JSON Parsing funcationality. 

public class ApiHit
{
    StringBuilder stringBuilder = new StringBuilder();
    String Url,Type="";
    JSONObject jsonObject;
    DataOutputStream printout;
    Activity activity;
    ApiResponse apiResponse;

    public ApiHit(Activity activity,String Url,String type,ApiResponse apiResponse)
    {

        this.activity=activity;
        this.Url=Url;
        this.activity=activity;
        this.Type=type;
        this.apiResponse=apiResponse;
        new ApiListener().execute();
    }

    public ApiHit(Activity activity,String Url,String type,JSONObject jsonObject,ApiResponse apiResponse)
    {
        this.activity=activity;
        this.Url=Url;
        this.jsonObject=jsonObject;
        this.activity=activity;
        this.Type=type;
        this.apiResponse=apiResponse;
        new ApiListener().execute();
    }



    public class ApiListener extends AsyncTask<String,String,String>
    {



        @Override
        protected String doInBackground(String... params)
        {
            try {
                //Log.e("Inside","Do in Background");
                URL url = new URL(Url);
                //Log.e("URL",Url);
                HttpURLConnection htp = (HttpURLConnection) url.openConnection();
                if(Type.equalsIgnoreCase("GET"))
                {
                    Log.e("Get","Get");
                    Log.e("URL",Url);
                    htp.setRequestMethod("GET");
                    htp.setRequestProperty("Content-Type", "application/json");
                    htp.setDoInput(true);
                    htp.setUseCaches(false);
                    htp.connect();
                }
                else
                {
                    Log.e("Post","Post");
                    Log.e("URL",Url);
                    Log.e("Params",jsonObject.toString());
                    htp.setRequestMethod("POST");
                    htp.setRequestProperty("Content-Type", "application/json");
                    htp.setDoInput(true);
                    htp.setDoOutput(true);
                    htp.setUseCaches(false);
                    htp.connect();
                    printout = new DataOutputStream(htp.getOutputStream());
                    printout.writeBytes(jsonObject.toString());
                    printout.flush();
                    printout.close();
                }
                InputStream inputStream = htp.getInputStream();
                BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(inputStream));
                String Line;
                while ((Line = bufferedReader.readLine()) != null) {
                    stringBuilder.append(Line);
                }
                return stringBuilder.toString();
            } catch (Exception e) {
                Log.e("Error",e.getMessage());
            }
            return "null";
        }

        @Override
        protected void onPostExecute(String s)
        {
            //Log.e("Response",s);
            apiResponse.onApiResponse(s);
        }
    }

}


 Step 2 : Create interface for Handle API Response.
 
 public interface ApiResponse
{
    public void onApiResponse(String response);
}


Step 3 : In The class we are showing How we are using, You can use as your requirement, We are JUST Show a example how you can use to Above two classes.


package com.omni.apilistner;

import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;

import com.omni.apilistner.ApiListeners.ApiHit;
import com.omni.apilistner.ApiListeners.ApiResponse;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);


        met();
        met2();
    }


    private void met(){

       new ApiHit(this, "PUT your GET URL", "GET", new ApiResponse() {
           @Override
           public void onApiResponse(String response) {

               Log.e("res",response);

           }
       });
    }

    private void met2(){
        new ApiHit(this, "PUT your GET URL", "GET", new ApiResponse() {
            @Override
            public void onApiResponse(String response) {

                Log.e("res2",response);

            }
        });
    }
    
     private void met3(){
        JSONObject jsonObject= new JSONObject();
        try {
            
            jsonObject.put("KEY1","value1");
            jsonObject.put("KEY1","value1");
            jsonObject.put("KEY1","value1");
            jsonObject.put("KEY1","value1");
        }catch (JSONException e)
        {
            e.printStackTrace();
        }
        
        
        
        new ApiHit(this, "PUT your POST API URL", "post", jsonObject,new ApiResponse() {
            @Override
            public void onApiResponse(String response) {

            }
        });
    }
}

