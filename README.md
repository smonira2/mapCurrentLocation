public class MapsActivity extends FragmentActivity implements 
OnMapReadyCallback,
 GoogleApiClient.ConnectionCallbacks,
 GoogleApiClient.OnConnectionFailedListener,
 LocationListener{
 private GoogleMap mMap;
 private GoogleApiClient client;
 private LocationRequest locationRequest;
 private Location lastlocation;
 private Marker currentLocationmMarker;
 public static final int REQUEST_LOCATION_CODE = 99;
 int PROXIMITY_RADIUS = 10000;
 double latitude,longitude;
 @Override
 protected void onCreate(Bundle savedInstanceState) {
 super.onCreate(savedInstanceState);
 setContentView(R.layout.activity_maps);
 if (Build.VERSION.SDK_INT >= 
Build.VERSION_CODES.LOLLIPOP) {
 checkLocationPermission();
 }
 // Obtain the SupportMapFragment and get notified when 
the map is ready to be used.
 SupportMapFragment mapFragment = (SupportMapFragment) 
getSupportFragmentManager()
 .findFragmentById(R.id.map);
 mapFragment.getMapAsync(this);
 }
 @Override
 public void onRequestPermissionsResult(int requestCode, 
@NonNull String[] permissions, @NonNull int[] grantResults) {
 switch(requestCode)
 {
 case REQUEST_LOCATION_CODE:
 if(grantResults.length >0 && grantResults[0] == 
PackageManager.PERMISSION_GRANTED)
 {
 
if(ContextCompat.checkSelfPermission(this,Manifest.permission.AC
CESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
 {
 if(client == null)
 {
 buildGoogleApiClient();
 }
mMap.setMyLocationEnabled(true);
 }
 }
else
 {
 Toast.makeText(this,"Permission Denied" , 
Toast.LENGTH_LONG).show();
 }
 }
 }
 
 @Override
 public void onMapReady(GoogleMap googleMap) {
 mMap = googleMap;
 if (ContextCompat.checkSelfPermission(this, 
Manifest.permission.ACCESS_FINE_LOCATION) == 
PackageManager.PERMISSION_GRANTED)
 {
 buildGoogleApiClient();
 mMap.setMyLocationEnabled(true);
 }
 }
 protected synchronized void buildGoogleApiClient()
 {
 client=new GoogleApiClient.Builder(this)
 .addConnectionCallbacks(this)
 .addOnConnectionFailedListener(this)
 .addApi(LocationServices.API)
 .build();
 client.connect();
 }
 @Override
 public void onLocationChanged(Location location) {
 latitude = location.getLatitude();
 longitude = location.getLongitude();
 lastlocation = location;
 if(currentLocationmMarker != null)
 {
 currentLocationmMarker.remove();
 }
 Log.d("lat = ",""+latitude);
 LatLng latLng = new LatLng(location.getLatitude() , 
location.getLongitude());
 MarkerOptions markerOptions = new MarkerOptions();
 markerOptions.position(latLng);
 markerOptions.title("Current Location");
 
markerOptions.icon(BitmapDescriptorFactory.defaultMarker(BitmapD
escriptorFactory.HUE_RED));
 currentLocationmMarker = mMap.addMarker(markerOptions);
 mMap.moveCamera(CameraUpdateFactory.newLatLng(latLng));
 mMap.animateCamera(CameraUpdateFactory.zoomBy(10));
 if(client != null)
 {
 
LocationServices.FusedLocationApi.removeLocationUpdates(client,t
his);
 }
 }
 
 @Override
 public void onConnected(@Nullable Bundle bundle) {
 locationRequest = new LocationRequest();
 locationRequest.setInterval(1000);
 locationRequest.setFastestInterval(1000);
 
locationRequest.setPriority(LocationRequest.PRIORITY_BALANCED_PO
WER_ACCURACY);
 if (ContextCompat.checkSelfPermission(this, 
Manifest.permission.ACCESS_FINE_LOCATION) == 
PackageManager.PERMISSION_GRANTED) {
 
LocationServices.FusedLocationApi.requestLocationUpdates(client,
locationRequest, this);
 }
 }
 public boolean checkLocationPermission() {
 
if(ContextCompat.checkSelfPermission(this,Manifest.permission.AC
CESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED )
 {
 if 
(ActivityCompat.shouldShowRequestPermissionRationale(this,Manife
st.permission.ACCESS_FINE_LOCATION))
 {
 ActivityCompat.requestPermissions(this,new 
String[] {Manifest.permission.ACCESS_FINE_LOCATION 
},REQUEST_LOCATION_CODE);
 }
 else
 {
 ActivityCompat.requestPermissions(this,new 
String[] {Manifest.permission.ACCESS_FINE_LOCATION 
},REQUEST_LOCATION_CODE);
 }
 return false;
 }
 else
 return true;
 }
 @Override
 public void onConnectionSuspended(int i) {
 }
 @Override
 public void onConnectionFailed(@NonNull ConnectionResult 
connectionResult) {
 }
}
Manifest: 
<uses-permission 
android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission 
android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission 
android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission 
android:name="com.google.android.providers.gsf.permission.READ_G
SERVICES" />
Gradle:
implementation 'com.google.android.gms:play-serviceslocation:15.0.1'
