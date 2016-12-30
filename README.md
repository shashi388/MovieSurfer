package com.example.admin.moviesurfers;

import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        if (savedInstanceState == null) {
            getFragmentManager().beginTransaction()
                    .add(R.id.container, new ForecastFragment(),"A")
                    .commit();
        }

    }
}

----------------------------------------------------------------------------------------------------------------------

package com.example.admin.moviesurfers;

import android.app.Fragment;
import android.content.Context;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.net.Uri;
import android.os.AsyncTask;
import android.os.Bundle;
import android.util.Log;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.AdapterView;
import android.widget.GridView;

import org.json.JSONArray;
import org.json.JSONException;
import org.json.JSONObject;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;
import java.util.Arrays;

/**
 * Created by admin on 07-12-2016.
 */

public class ForecastFragment extends Fragment implements AdapterView.OnItemClickListener {

    Adapterr baseAdapter;
    FetchWeatherTask fetchWeatherTask;
    Bitmap[] bitmaps=null;
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {


        View rootView = inflater.inflate(R.layout.forecast_fragment, container, false);

        // Get a reference to the ListView, and attach this adapter to it.
        GridView gridView = (GridView) rootView.findViewById(R.id.gridview1);

//        FetchWeatherTask fetchWeatherTask=new FetchWeatherTask();
//        fetchWeatherTask.execute();
        Bitmap[] bitmaps=sendBitmap(getActivity());
Log.v("check bitmaps val zero",""+bitmaps);
        baseAdapter=new Adapterr(getActivity(),bitmaps);


        gridView.setAdapter(baseAdapter);
        gridView.setOnItemClickListener(this);



        return rootView;
    }



    @Override
    public void onItemClick(AdapterView<?> adapterView, View view, int i, long l) {


    }


    public Bitmap[] sendBitmap(Context context)
    {
        FetchWeatherTask fetchWeatherTask=new FetchWeatherTask(getActivity());
        fetchWeatherTask.execute(context);
        try {
            Thread.sleep(10000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        Log.v("check bitmaps val zero2",""+bitmaps);


        return bitmaps;
    }

    @Override
    public void onStart() {
        super.onStart();
Log.v("onStart()","in the start method");
//        FetchWeatherTask fetchWeatherTask=new FetchWeatherTask();
//        fetchWeatherTask.execute();
    }

    public class FetchWeatherTask extends AsyncTask<Context, Bitmap[], Bitmap[]>
    {
        private final String LOG_TAG = FetchWeatherTask.class.getSimpleName();

        public FetchWeatherTask(Context context) {
            super();
        }


        /* The date/time conversion code is going to be moved outside the asynctask later,
         * so for convenience we're breaking it out into its own method now.
         */
        /**
         * Prepare the weather high/lows for presentation.
         */

        /**
         * Take the String representing the complete forecast in JSON Format and
         * pull out the data we need to construct the Strings needed for the wireframes.
         *
         * Fortunately parsing is easy:  constructor takes the JSON string and converts it
         * into an Object hierarchy for us.
         */
        private Bitmap[] getWeatherDataFromJson(String forecastJsonStr, int numDays)
                throws JSONException {

Log.v("forecastJsonStr",""+forecastJsonStr);
            Log.v("Mymsg11","hey i am in method");
            // These are the names of the JSON objects that need to be extracted.
            final String OWM_LIST = "results";
            final String OWM_WEATHER = "weather";
            final String OWM_TEMPERATURE = "temp";
            final String OWM_MAX = "max";
            final String OWM_MIN = "min";
            final String POSTER_PATH = "poster_path";

            JSONObject forecastJson = new JSONObject(forecastJsonStr);
            JSONArray weatherArray = forecastJson.getJSONArray(OWM_LIST);

            // OWM returns daily forecasts based upon the local time of the city that is being
            // asked for, which means that we need to know the GMT offset to translate this data
            // properly.

            // Since this data is also sent in-order and the first day is always the
            // current day, we're going to take advantage of that to get a nice
            // normalized UTC date for all of our weather.


            // we start at the day returned by local time. Otherwise this is a mess.
            String[] returnUrl = new String[weatherArray.length()];

            String[] resultStrs = new String[weatherArray.length()];
            for (int i = 0; i < weatherArray.length(); i++) {
                // For now, using the format "Day, description, hi/low"

                // These two need to be declared outside the try/catch
                // so that they can be closed in the finally block.


                // Get the JSON object representing the day
                JSONObject dayForecast = weatherArray.getJSONObject(i);//@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Log.v("dayForecast",""+dayForecast);                // The date/time is returned as a long.  We need to convert that
                // into something human-readable, since most people won't read "1400356800" as
                // "this saturday".
//                long dateTime;
//                // Cheating to convert this to UTC time, which is what we want anyhow
//                // description is in a child array called "weather", which is 1 element long.
//                JSONObject weatherObject = dayForecast.getJSONArray(OWM_WEATHER).getJSONObject(0);//++@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@++++++++++++())()((&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
//                description = weatherObject.getString(OWM_DESCRIPTION);


               // String poster_path = dayForecast.getJSONObject(POSTER_PATH);//++@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@++++++++++++())()((&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&

//Log.v("poster_path2",""+poster_path);

                String poster = dayForecast.getString(POSTER_PATH);
Log.v("poster_path",""+poster);
Log.v("poster",""+poster);
                // Temperatures are in a child object called "temp".  Try not to name variables
                // "temp" when working with temperature.  It confuses everybody.
                resultStrs[i] = poster;


                //++++++++++++++++++++++++++++++++++++
                HttpURLConnection urlConnection = null;
                BufferedReader reader = null;

                final String FORECAST_BASE_URL =
                        "https://image.tmdb.org/t/p/w500/";

                String imgUrl=FORECAST_BASE_URL+poster;
                returnUrl[i] = imgUrl;

                String hh=returnUrl[i];
Log.v("resultUri[i]",""+hh);

            }

            Log.v("resultUriARRAY",""+ Arrays.toString(returnUrl));


            bitmaps=new Bitmap[numDays];


            for(int i=0;i<bitmaps.length;i++) {

//

                try {
                    Log.v("dfgsdsghjjjjjj","kkkkkkkkkk");

                    URL url = new URL(returnUrl[i]);
                    Log.v("dfgsdsghjjjjjj","kkkkkkkkkk");

                    bitmaps[i] = BitmapFactory.decodeStream(url.openConnection().getInputStream());
                    Log.v("donnnnn","OK");

                } catch(IOException e) {
                    Log.v("dfgsdsghjjjjjj","kkkkkkkkkk");

                }
                Log.v("donnnnn","OK");

            }

Log.v("returning the bitmap",""+bitmaps);

return bitmaps;

        }



        @Override
        protected Bitmap[] doInBackground(Context...contexts) {

            // If there's no zip code, there's nothing to look up.  Verify size of params.
//            if (params.length == 0) {
// Log.v("shiiiit man","stoping the initial lkevel");
//                return null;
//            }

            // These two need to be declared outside the try/catch
            // so that they can be closed in the finally block.
            HttpURLConnection urlConnection = null;
            BufferedReader reader = null;

            // Will contain the raw JSON response as a string.
            String forecastJsonStr = null;

            String format = "json";
            String units = "metric";
            int numDays = 20;



            try {
                // Construct the URL for the OpenWeatherMap query
                // Possible parameters are avaiable at OWM's forecast API page, at
                // http://openweathermap.org/API#forecast
                final String FORECAST_BASE_URL =
                        "https://api.themoviedb.org/3/movie/upcoming?";
                final String APPID_PARAM = "api_key";

                Uri builtUri = Uri.parse(FORECAST_BASE_URL).buildUpon()
                        .appendQueryParameter(APPID_PARAM, BuildConfig.OPEN_API_KEY)
                        .build();

                URL url = new URL(builtUri.toString());

 Log.v("Built URI ",""+builtUri.toString());

                // Create the request to OpenWeatherMap, and open the connection
                urlConnection = (HttpURLConnection) url.openConnection();
                urlConnection.setRequestMethod("GET");
                urlConnection.setDoInput(true);
                urlConnection.setDoOutput(true);

                Log.v("Built URI connection ",""+builtUri.toString());

                // Read the input stream into a String
                InputStream inputStream = urlConnection.getInputStream();

                Log.v("cheching....",""+inputStream);
                StringBuffer buffer = new StringBuffer();
                if (inputStream == null) {
                    // Nothing to do.
                    return null;
                }
                reader = new BufferedReader(new InputStreamReader(inputStream));

                String line;
                while ((line = reader.readLine()) != null) {
                    // Since it's JSON, adding a newline isn't necessary (it won't affect parsing)
                    // But it does make debugging a *lot* easier if you print out the completed
                    // buffer for debugging.
                    buffer.append(line + "\n");
                }
 Log.v("urlconnection",""+buffer.toString());
                if (buffer.length() == 0) {
                    // Stream was empty.  No point in parsing.
                    return null;
                }
                forecastJsonStr = buffer.toString();

Log.v("Forecast string: ",""+forecastJsonStr);
            } catch (IOException e) {
                Log.e(LOG_TAG, "Error ", e);
                // If the code didn't successfully get the weather data, there's no point in attemping
                // to parse it.
                return null;
            } finally {
                if (urlConnection != null) {
                    urlConnection.disconnect();
                }
                if (reader != null) {
                    try {
                        reader.close();
                    } catch (final IOException e) {
                        Log.e(LOG_TAG, "Error closing stream", e);
                    }
                }
            }

            try {
                 Bitmap[] arg=  getWeatherDataFromJson(forecastJsonStr, numDays);


                Log.v("logging man",""+arg);
                return arg;
            } catch (Exception e) {
                Log.v("logging man2222","sdfsfsasdfs"+e);
            }
Log.v("shiiiiiiiiit","geting null");

            // This will only happen if there was an error getting or parsing the forecast.
            return null;
        }


        @Override
        protected void onProgressUpdate(Bitmap[]... values) {
            super.onProgressUpdate(values);


        }


        @Override
        protected void onPostExecute(Bitmap[] result) {
            if (result != null) {
                Adapterr adapterr=new Adapterr(getActivity(),result);
               // adapterr.arrayList.clear();
                Log.v("Bitmapfdffdgydr",""+ Arrays.toString(result));

                }

        }
    }
}

--------------------------------------------------------------------------------------------------------------------------------
package com.example.admin.moviesurfers;

import android.content.Context;
import android.graphics.Bitmap;
import android.util.Log;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
import android.widget.ImageView;

import java.util.ArrayList;

/**
 * Created by admin on 07-12-2016.
 */

public class Adapterr extends BaseAdapter {


    ArrayList<MyHolder> arrayList;
    Bitmap[] bmp=null ;
    ArrayList<Bitmap>  arrBitmaps=new ArrayList<Bitmap>();


    Context context;
    Adapterr(Context context,Bitmap[] bmp)
    {
        this.context=context;
        this.bmp=bmp;
//        int[] img = {
//
//                R.drawable.img1,
//                R.drawable.img2,
//                R.drawable.img3,
//                R.drawable.img4,
//                R.drawable.img5,
//                R.drawable.img6,
//                R.drawable.img7,
//                R.drawable.img8,
//                R.drawable.img9,
//                R.drawable.img10
//
//        };
//
//        for(int i=0;i< 10;i++)
//        {
//
//                arrayList.add(new MyHolder(img[i]));
//
//        }
            for (int i = 0; i < 20; i++) {
                arrBitmaps.add(bmp[i]);
                Log.v("added in arraylist", "" + bmp[i]);
                Log.v("arraysize is ", "" + arrBitmaps.size());
            }



    }

    @Override
    public int getCount() {
Log.v("getCount()",""+arrBitmaps.size());        return arrBitmaps.size();
    }

    @Override
    public Object getItem(int i) {
        Log.v("getItem",""+arrBitmaps.get(i));
        return arrBitmaps.get(i);
    }

    @Override
    public long getItemId(int i) {
        Log.v("getItemId()",""+i);
        return i;
    }

    @Override
    public View getView(int i, View view, ViewGroup viewGroup) {

Log.v("got getView method","");
        LayoutInflater inflater=(LayoutInflater)context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
        View row=inflater.inflate(R.layout.single_img,viewGroup,false);
Log.v("inside getView","got row");
        ImageView imageView22=(ImageView)row.findViewById(R.id.imageView2);
if(arrBitmaps.get(i)==null) {

    Log.v("Bit","nulllllllll");
    //Log.v("Bll","");

   // MyHolder temp = arrayList.get(i);
   // imageView22.setImageResource(temp.img);
}else {
    Log.v("final bitmap ",""+arrBitmaps.get(i));
   // Adapterr adapterr = new Adapterr(context,bmp);
    imageView22.setImageBitmap(arrBitmaps.get(i));
}
        return row;

    }
}
-----------------------------------------------------------------------------------------------------------------------------------------

package com.example.admin.moviesurfers;

/**
 * Created by admin on 07-12-2016.
 */

public class MyHolder  {

    int img;

    MyHolder(int img)
    {
        this.img=img;
    }

}





